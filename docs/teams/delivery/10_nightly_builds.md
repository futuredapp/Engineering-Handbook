# Nightly Builds

Nightly builds are automated daily builds that streamline our delivery process by automatically building, distributing, and tracking development progress through Jira integration.

## Quick Start

Integrate the latest nightly build workflows from [futuredapp/.github](https://github.com/futuredapp/.github) repository into your project:

- **KMP projects:** [`kmp-futured-template/on_cron.yml`](https://github.com/futuredapp/kmp-futured-template/blob/develop/.github/workflows/on_cron.yml)
- **Android projects:** [`android-project-template-compose/nightly.yml`](https://github.com/futuredapp/android-project-template-compose/blob/main/.github/workflows/nightly.yml)
- **iOS projects:** [`iOS-project-template/build.yml`](https://github.com/futuredapp/iOS-project-template/blob/main/.github/workflows/build.yml)

## Overview

Nightly builds automate the build-test-deploy cycle and integrate with Jira to track issue progress. The system uses:

- **GitHub for Jira app** - Handles automatic transitions for branch creation, PR opening, and PR merging
- **Custom GitHub Action** - Transitions issues to "Testing" status when nightly builds succeed
- **Shared workflows** - Centralized in `futuredapp/.github` repository for consistency across all projects

### Benefits

**Resource Efficiency**

- Single nightly build accumulates all changes from the day
- Reduces unnecessary build cycles compared to building after each merge
- Optimizes CI/CD resource usage

**Status Accuracy**

- Issues transition to "Testing" only after successful builds
- Prevents QA from attempting to test features before builds are available
- "Development Done" status bridges the gap between code merge and build availability

## How It Works

The complete development process with automated Jira transitions:

```
To Do
  │
  │ (Branch created in GitHub) ← Handled by Jira workflow trigger
  ↓
In Progress
  │
  │ (PR opened) ← Handled by Jira workflow trigger
  ↓
Review
  │
  │ (PR merged to develop) ← Handled by Jira workflow trigger
  ↓
Development Done
  │
  │ (Nightly build succeeds) ← Handled by GitHub Action calling Jira API
  ↓
Testing
  │
  │ (Manual QA complete) ← Manual transition after QA done
  ↓
Done
```

**Jira workflow transitions triggered by GitHub events:**

- **Branch created** → "In Progress" (triggered by GitHub for Jira app)
- **PR opened** → "Review" (triggered by GitHub for Jira app)
- **PR merged to develop** → "Development Done" (triggered by GitHub for Jira app)
- **Nightly build succeeds** → "Testing" (triggered by custom GitHub Action via Jira REST API)

**Jira workflow transitions handled internally:**

- **Testing** → "Done" (manual transition or Jira automation rules)

### How the Custom GitHub Action Works

The custom GitHub Action automates the transition from "Development Done" to "Testing":

1. Identifies all commits merged to the `develop` branch since the last successful build
2. Extracts branch names from these merge commits
3. Uses pattern matching to find Jira issue keys in branch names
4. Transitions all found issues to the specified status on successful build

**Example:** If you merge branches `feature/PROJ-123-login` and `feature/PROJ-456-profile`, both issues `PROJ-123` and `PROJ-456` will be transitioned to "Testing" when the nightly build succeeds.

## GitHub Workflow Setup

### Available Shared Workflows

The [futuredapp/.github](https://github.com/futuredapp/.github) repository provides three nightly build workflows with Jira integration:

- **[`android-cloud-nightly-build.yml`](https://github.com/futuredapp/.github/blob/main/.github/workflows/android-cloud-nightly-build.yml)** - For Android projects
- **[`ios-selfhosted-nightly-build.yml`](https://github.com/futuredapp/.github/blob/main/.github/workflows/ios-selfhosted-nightly-build.yml)** - For iOS projects
- **[`kmp-combined-nightly-build.yml`](https://github.com/futuredapp/.github/blob/main/.github/workflows/kmp-combined-nightly-build.yml)** - For Kotlin Multiplatform projects

All workflows use custom GitHub Actionss that automatically:

1. Detect merged branches since the last successful build
2. Allow skip the entire build if there were no changes (saves resources)
3. Extract Jira issue keys from branch names (e.g., `PROJ-123` from `feature/PROJ-123-task-name`)
4. Transition those issues via Jira REST API on build success

### Configuring Jira Integration

To enable Jira integration in your nightly build workflow, add these parameters:

```yaml
jobs:
  nightly:
    uses: futuredapp/.github/.github/workflows/[your-workflow].yml@main
    with:
      JIRA_TRANSITION: 'Testing'  # Transition to apply on successful build
      # ... your other project-specific inputs
    secrets:
      JIRA_CONTEXT: ${{ secrets.JIRA_CONTEXT }}  # Organization secret
```

**Required Configuration:**

- `JIRA_TRANSITION` - The Jira status name to transition issues to (typically "Testing")
- `JIRA_CONTEXT` - Organization-level secret containing Jira API credentials (already configured)

The Jira integration is powered by the [`jira-transition-tickets`](https://github.com/futuredapp/.github/tree/main/.github/actions/jira-transition-tickets) custom GitHub Action, which handles the automatic issue transitions.

### Complete Workflow Example

Here's a complete example showing triggers, version generation, and Jira integration:

```yaml
name: Nightly build

on:
  schedule:
    - cron: "0 2 * * *"  # Runs at 2 AM daily
  workflow_dispatch:      # Allows manual builds
    inputs:
      version_name:
        description: 'Version name'
        required: true
        type: string
        default: '1.x.x-snapshot'

jobs:
  generate_version:
    runs-on: ubuntu-latest
    outputs:
      version_name: ${{ steps.version.outputs.version_name }}
    steps:
      - name: Generate version name
        id: version
        run: |
          if [ "${{ github.event_name }}" == "workflow_dispatch" ]; then
            VERSION_NAME="${{ inputs.version_name }}"
          else
            VERSION_NAME="1.x.x-nightly"
          fi
          echo "version_name=$VERSION_NAME" >> $GITHUB_OUTPUT

  nightly_build:
    needs: generate_version
    uses: futuredapp/.github/.github/workflows/kmp-combined-nightly-build.yml@2.1.0
    with:
      JIRA_TRANSITION: 'Testing'
      # ... additional project-specific configuration
    secrets:
      JIRA_CONTEXT: ${{ secrets.JIRA_CONTEXT }}
      # ... additional project-specific secrets
```

**Key components:**

- **Scheduled trigger** (`cron`) - Runs automatically at specified time
- **Manual trigger** (`workflow_dispatch`) - Allows on-demand builds with custom version
- **Version generation** - Dynamic version naming based on trigger type
- **Shared workflow reference** - Uses centralized workflow from `.github` repository

## Branch Naming Convention

!!! warning "Branch name formatting"

    Branch names must contain the Jira issue key for the automation to work correctly.

### Valid Formats

The Jira issue key (e.g., `PROJ-123`) can appear anywhere in the branch name:

- `feature/PROJ-123-task-name`
- `fix/PROJ-123-bug-description`
- `PROJ-123-feature-name`
- `bugfix/PROJ-123-fix-login`
