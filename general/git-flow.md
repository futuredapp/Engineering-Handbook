# Git flow

## Branches

We use `develop` as the main branch to which the development increments (pull requests) are being integrated.

The `main` branch is the stable branch, keeping the most recently released version. Each merge to `main` should be appropriately tagged with build number of the release (see [Release checklist - iOS](../ios/releases.md)).

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

Commit messages should be in the imperative present tense, start with a capital letter and have no period at the end. Keep your commits short. For more guidance read [this blog post](https://chris.beams.io/posts/git-commit/).

```
// Preferred
Add employee avatar in list of employees

// Not preferred
Added image view to display image for employee in employees table view controller.
```

## Pull requests

All pull request should point to `develop` branch, except `release/` and `hotfix/`, which are intended to `main`.

Attachments and more detailed information about the PR is welcomed, such as:

* A screenshot of an UI, if PR implements or fixes it
* A gif image if an animation is part of the PR
* Text description of what reviewers should pay attention to during code review

### Code review

Code review is obligatory before merging the pull request. A pull request should get at least one approval from another member of a team to get merged. If possible, cross-reviewing from a member of a different team is preferred.

While doing code review, be nice and respect the work of another team member. Pay attention to the functional, structural and style issues. GitHub offers some of a good advice on offering and responding to feedback [in their blog post](https://blog.github.com/2015-01-21-how-to-write-the-perfect-pull-request/).

### Continuous Integration

Another required part to merge a PR is successful build on the CI. Pull request with failing build is not possible to merge. CI also sends [Danger](http://danger.systems/ruby/) report to the PR, which checks both git flow rules enforcements and code style issues. Danger rules in use on our projects are available in [futuredapp/danger]( https://github.com/futuredapp/danger/) repository.

Danger reports issues and errors. Errors must be fixed before merging the PR, issues are strongly recommended to fix.
