# Git Flow

## Branches

We use `develop` as the main branch to which the development increments (pull requests) are being integrated.

The `main` branch is the stable branch, keeping the most recently released version. Each merge to `main` should be appropriately tagged with build number of the release (see [Release](ios_release.md#source-control-steps-to-release)).

Feature branches, prefixed with `feature/`, are used to implement one specific feature defined in Jira task. When a feature is merged and a fix is required by Jira task (eg. from tester), a `fix/` branch is used. Similarly, a `hotfix/` branch is used if urgent fix of a released version is needed and should be merged only to the `main` branch.

Branches prefixed with `feature/`, `fix/` and `hotfix/` require Jira task ID and a short description, as defined by the following regular expression `(feature|hotfix|fix)\/([A-Z]{2,}-\d+)`.

For housekeep commits such as a dependency update, a `housekeep/` branch should be used. Releases should be prepared in a `release/` branch, which should then be merged to `main`.

`main` and `develop` branches are protected and merging to them is only available using the pull request.

Examples of pretty branch names:

* `feature/JIRA-00-chat-screen`
* `fix/JIRA-00-sending-messages`
* `release/v0.0.0-0`
* `housekeep/dependencies`
* `hotfix/JIRA-00-crash-on-launch`


## Commits

Commit messages follow [Conventional Commits](https://www.conventionalcommits.org): `type(scope): description`. Write the description in the imperative present tense, keep the subject under 50 characters with no period at the end; wrap an optional body at 72 characters. Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`. The scope is the affected area (`auth`, `ui`, `api`, …). Keep your commits short and focused. The `/commit` skill from the Futured Claude marketplace generates messages in this format.

```
// Preferred
feat(employees): add avatar to employee list

// Not preferred
Added image view to display image for employee in employees table view controller.
```

## Pull requests

All pull request should point to `develop` branch, except `release/` and `hotfix/`, which are intended to `main`.

Every pull request carries an `## Evidence` section: screenshots or a screen recording for UI changes, a test that fails without the fix for bug fixes. The `/pull-request` skill writes it with you.

### Code review

A pull request does not need an approval from a colleague. The AI review bot reviews every push, and a human reviews only red-zone PRs and PRs labeled `needs-human` (whenever the author asks). Finished features are reviewed in the app instead of in the diff. See [Code Review](ios_code_review.md) for the whole process.

When you do review, be nice and respect the work of another team member.

### Continuous Integration

Another required part to merge a PR is successful build on the CI, and the `review-gate` check. Pull request with failing build is not possible to merge.
