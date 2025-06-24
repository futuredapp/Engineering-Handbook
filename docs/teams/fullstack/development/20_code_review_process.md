A thorough code review process helps us maintain high quality, consistency, and reliability in our codebase. The following guidelines outline our expectations and workflow for reviewing pull requests:

### Review Requirements

- **Approval**  
  Every pull request must receive at least one approving review before it can be merged.

- **Passing Checks**  
  All automated PR checks (CI, Dangerfile, tests, etc.) must pass before merging.

- **Draft PRs**  
  Draft pull requests are welcome for early feedback, but they are not reviewed until marked as "Ready for Review."

- **Comment Resolution**  
  All reviewer comments must be addressed and resolved before merging. If a discussion is ongoing, the PR should not be merged.

### Reviewer Assignment

- **Assigning Reviewers**  
  The PR author is responsible for assigning at least one appropriate reviewer, ideally someone familiar with the relevant codebase or feature area.

### Review Best Practices

- **Test Significant Changes**  
  For larger or riskier PRs, reviewers should check out the branch and run the code locally to verify functionality.

- **Ask for Clarification**  
  If any part of the code is unclear, reviewers should ask questions or request additional explanations. Authors are expected to respond promptly and clarify as needed.

- **Leverage AI Tools**  
  Reviewers are encouraged to use Copilot and CodeRabbit to gain automated insights, suggestions, and code analysis during the review process.

### Merging Pull Requests

- **Small/Solo Projects:**  
  You may use **rebase and merge** to keep a linear commit history.

- **Medium and Large Collaborative Projects:**  
  Use **merge** or **squash and merge** to preserve context and simplify history. Choose the method that best fits the team’s workflow and the nature of the changes.

### Additional Guidelines

- **Respectful and Constructive Feedback**  
  All feedback should be respectful, specific, and focused on the code, not the coder.

- **Continuous Improvement**  
  Use code reviews as an opportunity to share knowledge, reinforce best practices, and help each other grow as engineers.

### Force Pushing Policy

- **No Force Pushes to Shared Branches**  
  Never force push (`git push --force` or `--force-with-lease`) to shared branches such as `main`, `develop`, or `staging`. Force pushing to shared branches can overwrite teammates’ work and disrupt the project history, leading to confusion and potential data loss.

- **Feature and Bugfix Branches**  
  Force pushing is allowed only on your own feature or bugfix branches if necessary (e.g., after a rebase or to clean up commit history). Before force pushing, ensure no one else is collaborating on your branch and communicate if needed.

### GitHub Branch Protection and PR Rules

- **Branch Protection Rules**  
  We configure GitHub branch protection rules to enforce our workflow and safeguard important branches:
    - Require at least one approving review before merging.
    - Require all status checks to pass before merging.
    - Prevent force pushes to protected branches (`main`, `develop`, `staging`).
    - Require branches to be up to date before merging.
    - Optionally, restrict who can push to or merge into protected branches.

- **Pull Request Settings**  
  In repository settings, we enable options such as:
    - Allowing merge, squash, or rebase merges according to project size and needs.
    - Using PR templates for consistency.
    - Enforcing code review limits so only authorized collaborators can approve or request changes.

- These rules help maintain a stable and reliable codebase, reduce risk of errors, and ensure a smooth collaboration process for all contributors.

By following this process, we ensure that every change merged into our codebase is reviewed, tested, and aligned with our standards.
