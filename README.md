# Pull Request Generator Script

This shell script is designed to streamline the process of generating pull request (PR) descriptions and templates. It interacts with your Git repository, retrieves commit information, and uses the OpenAI API to generate a descriptive PR template. Additionally, it includes an interactive checklist and a section for considerations to ensure a comprehensive PR submission.

---

## Features

1. **Automatic API Key Retrieval**: 
   - Fetches the OpenAI API key securely from the macOS Keychain.
   - Provides guidance for setting up the key if not found.

2. **Commit Analysis**:
   - Identifies forward commits between the current branch and the target branch.
   - Includes commit messages in the PR description.

3. **Interactive Checklist**:
   - Presents a set of predefined checklist items.
   - Allows marking all checklist items as "yes" by default or answering individually.

4. **Considerations Section**:
   - Asks whether there are any considerations for the PR.
   - Gathers additional details if considerations exist (e.g., migration requirements, fallbacks, dependencies).

5. **PR Template Generation**:
   - Creates a `PR_TEMPLATE.md` file with the gathered information.
   - Includes JIRA issue ID, branch details, commit summary, checklist, and considerations.

---

## Requirements

- **Git**: Ensure the script is run in a Git repository.
- **OpenAI API Key**: 
  - Must be added to the macOS Keychain using the following command:
    ```bash
    security add-generic-password -a "$USER" -s "OpenAI_API_Key" -w "your_api_key"
    ```
- **macOS**: This script is tailored for macOS systems using the Keychain for secure credential storage.
- **Bash**: The script requires `bash` to execute.

---

## Installation

1. Clone or download the script to your local machine.
2. Make the script executable:
   ```bash
   chmod +x generate_pr_template.sh
