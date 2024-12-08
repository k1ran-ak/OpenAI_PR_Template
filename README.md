#!/bin/bash

# Function to fetch API key from macOS Keychain
get_api_key() {
  security find-generic-password -a "$USER" -s "OpenAI_API_Key" -w 2>/dev/null
}

# Retrieve API key
api_key=$(get_api_key)

if [ -z "$api_key" ]; then
  echo "Error: API key not found in Keychain. Please add it using:"
  echo 'security add-generic-password -a "$USER" -s "OpenAI_API_Key" -w "your_api_key"'
  exit 1
fi

# Get the current branch name
current_branch=$(git rev-parse --abbrev-ref HEAD)

if [ -z "$current_branch" ]; then
  echo "Error: Could not determine the current branch. Ensure you are in a Git repository."
  exit 1
fi

# Set the default target branch (change 'main' if your default branch differs)
target_branch="main"

# Extract JIRA issue ID (part before the first '/')
jira_issue_id=$(echo "$current_branch" | cut -d '/' -f 1)

# Get the last 5 commit messages for the current branch
commit_messages=$(git log "$target_branch..$current_branch" --oneline)

if [ -z "$commit_messages" ]; then
  echo "Error: No recent commits found for the branch '$current_branch'."
  exit 1
fi

## Escape commit messages for valid JSON
#escaped_commit_messages=$(echo "$commit_messages" | jq -R . | jq -s .)

# Combine the commit messages into a single string
formatted_commit_messages=$(echo "$commit_messages" | tr '\n' ' ')

echo "Target Branch: $target_branch"
echo "Current Branch: $current_branch"

# Generate PR description using ChatGPT API
echo "Generating PR description..."
response=$(curl -s -X POST https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $api_key" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant for writing pull request descriptions."},
      {"role": "user", "content": "For the given commit messages generate a Pull request description, avoid using commit messages exactly rather give a meaningful yet proper PR description, description should be in points - '"$formatted_commit_messages"'. "}
    ],
    "temperature": 1,
    "max_tokens": 2048,
    "top_p": 1,
    "frequency_penalty": 0,
    "presence_penalty": 0
  }')

description=$(echo "$response" | jq -r '.choices[0].message.content')

if [ -z "$description" ] || [ "$description" == "null" ]; then
  echo "Error: Failed to generate PR description. Response from API:"
  echo "$response"
  exit 1
fi

# Create PR template
cat > PR_TEMPLATE.md <<EOL
# Description
JIRA issue: $jira_issue_id
<!--github automatically converts JIRA IDs into clickable links-->
<!--Add reference documentations and description of changes in this PR that gives additional context to reviewers-->
Branch: \`$current_branch\`
Merging into: \`$target_branch\`

$description

### Visual reference
<!--Add screenshots, video recording or other visual reference for changes if applicable-->

# How to Test
<!--Add testing steps needed to verify changes-->

# Considerations (if any)
- Does it require any major changes in the consumer
- Do we need Migration. If so:
    - Type of Migration (RESYNC / FORCELOGOUT)
    - Why this migration is required?
- Is there any specific fallback from those changes (revert / feature flag / other)
- Are there any inter-project dependencies

# Checklist
- [X] New and updated code is logically covered with unit tests and does not violate other product requirements
- [X] New and updated code do not trigger linter warnings or errors (Rules)
- [X] Changes are made according to Organization Style guide and other Coding Standards ([link](https://github.com/Adaptavant/Anywhere-IOS-Container/blob/main/Documentation/Code%20Standard.md#ios-code-standard))
- [X] Required documentation is added and/or updated (Inline or README)
- [X] New and updated strings are using localized keys (are not hardcoded)
- [X] New extension/helper/code is not duplicated
- [X] Required Sentry Logs (or) Breadcrumbs are added
- [X] Did I remove all unnecessary logging and print statements
- [X] Thread-Safety is considered when utilizing Shared resources
- [X] The code has been thoroughly reviewed for potential security vulnerabilities
- [X] Sensitive information is not exposed in code or configuration
EOL

cat PR_TEMPLATE.md | pbcopy
echo "PR copied to clipboard. Paste it in the GitHub PR form."
