# CI/CD

Projects created from our templates have default GitHub Actions configuration that contains workflows for pull request checks, Enterprise build distribution, and release on Google Play Store.

Default configuration uses [shared workflows](https://github.com/futuredapp/.github) for this purpose.

## Workflow Overview

The CI/CD setup consists of three main workflows:

1. **PR Check Workflow** - Runs on every pull request
2. **Nightly build workflow** - Runs once per day
3. **Release Workflow** - Runs when a GitHub release is created

## Android Native Projects

### PR Check Workflow

**[PR Check workflow](https://github.com/futuredapp/.github/blob/main/.github/workflows/android-cloud-check.yml)** - This workflow is triggered every time a pull request is created and doesn't require any additional configuration other than providing correct project-specific inputs such as name of test and lint Gradle tasks. The main purpose of this workflow is to run lint checks and unit tests. If major shortcomings are found or unit tests are not passing, this workflow will fail and PR merge will be blocked until it's resolved.

**What it does:**

- Runs linting checks
- Executes unit tests
- Validates code quality
- Blocks PR merge if checks fail

### Nightly build workflow

- **[Android Nightly build workflow](https://github.com/futuredapp/.github/blob/main/.github/workflows/android-cloud-nightly-build.yml)**
- **[KMP Nightly build workflow](https://github.com/futuredapp/.github/blob/main/.github/workflows/kmp-combined-nightly-build.yml)**

This workflow is designed to be triggered by cron, usually once per day at night, with the option to be triggered manually by GitHub's `workflow_dispatch` event. If there were any new commits since last build, it produces an Enterprise build (internal testing build) and publishes it to Firebase App Distribution for testing.

**Required Setup:** See [Setup Requirements](#setup-requirements) section.

**What it does:**

- Calls a custom Action to determine if there are any new merge commits since last successful build
- Skips build if there are no changes
- Produces a changelog based on extracted merge commits
- Builds the app in enterprise build variant
- Signs the APK with development key
- Uploads to Firebase App Distribution
- Persists SHA of last successful build into cache for the next run
- Transitions relevant Jira issues (based on changelog) into 'Testing' status (or any other status provided) using custom Action

### Release Workflow

The release workflow is triggered when a GitHub release is created. It builds a production-ready app bundle and uploads it to Google Play Console.

**Required Setup:** See [Setup Requirements](#setup-requirements) section

**What it does:**

- Builds signed release AAB
- Uploads to Google Play Console

## KMP (Kotlin Multiplatform) Projects

Multiplatform projects contain similar workflows as native projects with additional optimization for change detection.

### Change Detection

Before running the PR Check workflow, KMP projects use a **[detect changes](https://github.com/futuredapp/.github/blob/main/.github/workflows/kmp-cloud-detect-changes.yml)** job to determine if Android/KMP code has been modified. This prevents unnecessary CI/CD runs when only iOS code has changed.

**How it works:**

- Analyzes changed files in the PR
- Checks if Android/KMP source files were modified
- Only triggers Android workflows if relevant changes are detected
- Saves GitHub Actions runner time and resources

### Workflow Differences

- **PR Check Workflow**: Same as native projects, but only runs if Android/KMP changes are detected
- **Nightly build workflow**: Same as native projects, but includes jobs for both Android and iOS
- **Release Workflow**: Same as native projects, but includes jobs for both Android and iOS

## Setup Requirements

### For Nightly build workflow

!!! info "About Permissions"

    If you don't have necessary permissions to perform any of the steps described below, feel free to ask your tech leader for permissions and/or help.

In addition to required inputs:

- `APP_DISTRIBUTION_SERVICE_ACCOUNT` secret in GitHub repository settings
- Firebase project configured (see [Firebase Setup](./30_firebase.md))

![](../Resources/project_setup/secrets_config.png)

### For Release Workflow

In addition to required workflow inputs:

- Complete setup described in [Release CI/CD Guide](../release/20_ci_cd.md)
- Google Play Console access
- App signing keys and passwords
- Google Cloud service account
