# GitHub settings
Let's set up a GitHub repository for your project.

## General settings
First off, lets start by configuring basic repository settings in `Settings > General`:

1. Create a `develop` branch and set it as default branch.
2. Disable all features: Wikis, Issues, Sponsorships, Discussions and Projects

    ![](../../../assets/android/android_github_features.png)

3. Pull Request settings: 
    - Enable `Allow merge commits`

    ![](../../../assets/android/android_github_pull_requests.png)

    - Enable `Always suggest updating pull request branches`
    - Enable `Allow auto-merge`
    - Enable `Automatically delete head branches`

    ![](../../../assets/android/android_github_merging.png)

## Branch Rulesets

Branch rulesets are important for protecting your codebase and ensuring code quality. They prevent accidental or unauthorized changes to `develop` branch and `main` branches.

What is required:

- Pull requests as only way to merge new code to any of protected branches.
- Pull requests to be reviewed by at least one person (this rule can be adjusted based on project).
- Status checks (CI) to pass before merging.

### Configuration

Open up `Settings > Rules` and click `New Ruleset > New branch ruleset`

1. Name: "Pull Requests" (or something more suitable if you're creative)
2. Target branches:
    - Default branch (Include default branch)
    - `main` branch (Include by pattern)
3. Branch rules
    - Enable `Restrict deletions`
    - Enable `Require a pull request before merging`. Then under `Additional settings`:
        - Set Required approvals: `1` or more (this might be project-specific)
        - Enable `Dismiss stale pull request approvals when new commits are pushed` (this might be project-specific)

    - Enable `Require status checks to pass`. Then under `Additional settings`:
        - Select all CI status checks that run for pull requests
    - Enable `Block force pushes`

    ![](../../../assets/android/android_github_protection_prs.png)
    ![](../../../assets/android/android_github_protection_ci.png)
