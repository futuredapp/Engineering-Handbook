We use a structured Git branching model to ensure smooth collaboration and maintain a clean, reliable codebase. Our workflow is based on the following branches and conventions:

### Main Branches

- **`main`**  
  The production-ready branch. Code here is always stable and deployable to our production environment.
- **`develop`**  
  The integration branch for ongoing development. All feature branches are merged here before being promoted to `main`.
- **`staging`**  
  Optional branch for pre-production testing. It serves as a staging area for features before they are merged into `main`. Only used if project requires testing with production-like data.

### Feature Branches

- **Branch Naming**  
  Feature branches follow the pattern:  
  `feature/<jira-ticket-number>-<short-description-in-kebab-case>`  
  Example: `feature/ABC-123-user-authentication`
- **Source Branch**  
  Feature branches are created from the latest `develop` branch.

### Bugfix Branches

- **Branch Naming**  
  Bugfix branches follow the pattern:  
  `bugfix/<jira-ticket-number>-<short-description-in-kebab-case>`  
  Example: `bugfix/ABC-123-fix-login-error`
- In case there is no Jira ticket, the branch name should be descriptive of the bug being fixed.
- **Source Branch**  
  Bugfix branches are created from the branch where the bug was discovered (usually `develop`).

### Production Hotfixes

- **Branch Naming**  
  Hotfix branches follow the pattern:  
  `hotfix/<jira-ticket-number>-<short-description-in-kebab-case>`  
  Example: `hotfix/ABC-124-resolve-db-timeout`
- **Source Branch**  
  Hotfix branches are created from the `main` branch to address critical issues in production.
- **Procedure**  
  After the hotfix is tested and approved, it is merged back into both `main` and `develop` to ensure the fix is included in future releases.

### Commit Messages

- **Language**  
  All commit messages must be written in English.
- **Jira Integration**  
  Ideally, each commit message should include the Jira ticket number at the start (e.g., `ABC-123: Update user authentication`).
- **Clarity**  
  Commit messages should be clear and concise, explaining the purpose of the change. We do not use emojis in commit messages.
- **Semantic Commit Messages**  
  We also allow semantic commit messages, which follow the format:  
  `<type>(<scope>): <description>`, for example: `feat(auth): add user login functionality`. These should also include the Jira ticket number if applicable.



### Automated Checks

- **Dangerfile**  
  We use a Dangerfile to automatically check commit and pull request parameters, ensuring adherence to our standards.

### Release Tags

- **Version Tags**  
  Optionally, we tag commits with release version numbers (e.g., `v1.2.0`) to mark stable points in our codebase.


### Environments

- **Development, Staging, and Production**  
  We maintain separate environments for development, staging, and production.  
  The `develop` branch is typically deployed to the development environment (`dev`), and the `main` branch is deployed to production (`prod`). Optionally, a `staging` branch can be used and deployed to the staging environment.