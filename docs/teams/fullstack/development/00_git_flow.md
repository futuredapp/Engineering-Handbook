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
- **Projects with Automated Releases**  
  In projects based on our full-stack template, the semantic format is **required**, not optional — the version bump and release notes are generated from it. See [Releases & Versioning](#releases-versioning) below.

### Automated Checks

- **Dangerfile**  
  We use a Dangerfile to automatically check commit and pull request parameters, ensuring adherence to our standards.

### Releases & Versioning

Projects based on our full-stack template release automatically. Merging into `main` triggers the `Release` workflow (`.github/workflows/release.yml`), which runs [semantic-release](https://www.npmjs.com/package/@semantic-release/npm) — it reads the commit messages since the last release, decides the version, creates the git tag and publishes a GitHub Release. Nobody tags manually.

- **Versioning**
  We follow [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`
    - `MAJOR` — Breaking changes
    - `MINOR` — New features, backward-compatible
    - `PATCH` — Bug fixes

  The bump is **derived from your commit messages**, not chosen by a human. Tags use the format `v${version}`, e.g. `v1.2.0`.

- **Commit Types and the Version Bump**
  Only [Conventional Commits](https://www.conventionalcommits.org/) affect the release:

    | Commit | Release |
    |---|---|
    | `fix: ...` | Patch — `1.2.0` → `1.2.1` |
    | `feat: ...` | Minor — `1.2.0` → `1.3.0` |
    | `feat!: ...` or `BREAKING CHANGE:` in the commit body | Major — `1.2.0` → `2.0.0` |
    | `chore:`, `docs:`, `refactor:`, `test:`, `ci:`, `style:`, `perf:`, `build:` | No release |

  A commit that does not match the format is **silently ignored** — it will not appear in the release notes and will not influence the version. The Dangerfile warns about such commits on the pull request, but it does not block the merge, so it is on you to get them right. Watch out for an **empty scope**: `feat(): add thing` is invalid — write either `feat:` or `feat(auth):`. Jira ticket numbers go into the description, e.g. `feat(auth): ABC-123 add refresh token` (see [Commit Messages](#commit-messages) above).

- **What a Release Produces**
  A `vX.Y.Z` tag on `main` and a GitHub Release whose notes are grouped by commit type. The template deliberately keeps **no `CHANGELOG.md`** — the GitHub Releases page *is* the changelog, and the release commits nothing back to the repository. Nothing is published to any package registry.

#### Release Flow

1. `feature/*` and `bugfix/*` branches are merged into `develop` — no release happens here.
2. When `develop` is ready to ship, open a pull request from `develop` into `main`.
3. Merging it runs the `Release` workflow: tag, GitHub Release, business-friendly notes.
4. Production deployment follows from the same merge — see [Deployment Process](../deployment/00_deployment.md).

Hotfixes go from `hotfix/*` straight into `main` and produce their own release (usually a patch), and are then merged back into `develop`. Releases are serialized, so two merges in quick succession cannot race each other.

#### Configuration

The release setup lives in the repository root, in `.releaserc.json`:

```json
{
  "branches": ["main"],
  "tagFormat": "v${version}",
  "plugins": [
    ["@semantic-release/commit-analyzer", { "preset": "conventionalcommits" }],
    ["@semantic-release/release-notes-generator", { "preset": "conventionalcommits" }],
    ["@semantic-release/github", { "successComment": false, "failComment": false, "releasedLabels": false }]
  ]
}
```

The root `package.json` is private, holds the semantic-release dependencies and the `release` / `release:dry` scripts, and stays at version `0.0.0` — the real version lives only in the git tag. The repository is not a pnpm workspace: `api/` and `admin/` have their own `package.json` files and are versioned together by the single repository-level tag.

!!! note "Dry run"

    To see which version would be released without actually releasing it, run `pnpm release:dry` (`semantic-release --dry-run --no-ci`).

### Environments

- **Development, Staging, and Production**  
  We maintain separate environments for development, staging, and production.  
  The `develop` branch is typically deployed to the development environment (`dev`), and the `main` branch is deployed to production (`prod`). Optionally, a `staging` branch can be used and deployed to the staging environment.