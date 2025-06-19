# Git Flow

To ensure smooth collaboration on a project with a large team of developers and to ensure the correct functioning of our CI/CD and Jira integration tools, we have a small set of rules and recommendations to follow.

---

## Base Branches

**main** (formerly master):

  - The production branch, containing features and fixes that are available in the released version of the app.

**develop**:

  - Contains the latest features and fixes that are provided to QA for testing purposes. All features go to this branch before release.

---

## Feature and Fix Branches

### Branch Naming Convention
Feature and fix branches should follow a strict naming convention so our Jira integration works and tasks are moved automatically.

**Feature branch:**

  - Used when a new feature is developed.
  - Pattern: 
    ```
    feature/<jira-ticket-id>-<short-description-in-kebab-case>
    ```

**Fix branch:**

  - Used when the related Jira task is a bug task.
  - Pattern:
    ```
    fix/<jira-ticket-id>-<short-description-in-kebab-case>
    ```

**Hotfix branch:**

  - Used when we need to deploy a fix to already released code.
  - Pattern:
    ```
    hotfix/<jira-ticket-id>-<short-description-in-kebab-case>
    ```

> When a correctly named branch is pushed to GitHub, the related Jira task will be moved to the "in progress" state.

---

### Commit Message Convention
Commit messages should be written in English in present simple tense. They should start with one of the following prefixes (depending on the purpose of the commit), followed by a brief description:

- `Feat:` — prefix for feature commits (new feature is implemented or modified)
- `Fix:` — prefix for fix commits (fixing a bug task or fixing part of code that was not working as expected)
- `Chore:` — prefix for commits improving or fixing code style (no functional changes)

**Example:**
```
Feat: add pull to refresh implementation on dashboard screen
```

---

## Pull Request Convention

### Naming
Similarly to branch naming, pull request naming also contributes to Jira integration.

- **Pattern:**
  ```
  <jira-ticket-id> short description of the task
  ```

> When a correctly named Pull Request is created, the related Jira task is moved to the "Review" state. Once the PR is merged, the task is moved to the "Testing" state.

### Description
In general, the PR description is optional. If you want to help your colleagues with code review, you can include a description of the changes you have made and even add a few screenshots.

### Base Branch
- Feature and fix branches should be based on the `develop` branch, so the pull request should also target the `develop` branch.
- The hotfix branch, however, is created from the `main` branch and the PR should therefore target the `main` branch.
- After merging the hotfix PR into the `main` branch, it is necessary to merge the `main` branch into `develop` so that the hotfix is propagated to the entire codebase.

### PR Check
Our pull requests are automatically checked by GitHub Actions to ensure code quality and convention standards. If the PR does not comply with the rules, Danger will report the issues as a comment on the related PR, so the shortcomings can be fixed.




