# Description
JIRA issue: AWM-12235
<!--github automatically converts JIRA IDs into clickable links-->
<!--Add reference documentations and description of changes in this PR that gives additional context to reviewers-->
Branch: `AWM-12235/feature/on-demand-fetch-hotfix`
Merging into: `main`

### Pull Request Description

- Implementing a temporary solution to hide the toast message for a smoother user experience.
- Added a separate sentry tracker for improved error tracking and monitoring.
- Addressed issue AWM-12235 to enhance the overall stability of the application.

### Visual reference
<!--Add screenshots, video recording or other visual reference for changes if applicable-->

# How to Test
<!--Add testing steps needed to verify changes-->

### Considerations (if any)
- Does it require any major changes in the consumer: N/A
- Does it require Migration: N/A
    - Type of Migration: N/A
    - Why this migration is required: N/A
- Specific fallback (if any): N/A
- Inter-project dependencies: N/A

### Checklist
- [X] New and updated code is logically covered with unit tests and does not violate other product requirements
- [X] New and updated code do not trigger linter warnings or errors
- [X] Changes are made according to Organization Style guide and other Coding Standards
- [X] Required documentation is added and/or updated
- [X] New and updated strings are using localized keys (are not hardcoded)
- [X] New extension/helper/code is not duplicated
- [X] Required Sentry Logs (or) Breadcrumbs are added
- [X] Did I remove all unnecessary logging and print statements
- [X] Thread-Safety is considered when utilizing Shared resources
- [X] The code has been thoroughly reviewed for potential security vulnerabilities
- [X] Sensitive information is not exposed in code or configuration
