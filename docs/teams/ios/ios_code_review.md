# Code Review

A PR does not need an approval from a colleague. It needs an author who understands every line, a green AI review gate, and evidence that it works. People review where their judgment matters: risky changes, and finished features in the app.

## The flow

1. **The author owns the code.** You merge only what you understand and can explain. `/ios-review` is there to help you before opening the PR: conventions, patterns and code drift (duplicates, leftovers, custom code where a system component fits).
2. **Every PR carries evidence.** Screenshots or a screen recording for UI changes, a test that fails without the fix for bug fixes, "No behaviour change." for refactors, what and why for config. `/pull-request` writes the `## Evidence` section with you.
3. **The review bot comments once.** When a PR is opened or marked ready, the bot runs `/ios-pr-check` and posts a single comment: risk, whether the diff matches the description, evidence, and at most five verified serious findings plus code drift. Findings never block the merge; you decide what to fix.
4. **A human looks only at red zones.** When the change touches a red zone, the bot labels the PR `needs-human`, requests the project reviewer and lists the 1–3 places to look at. Add the label yourself whenever you want a second pair of eyes: an architecture decision, an SDK you have not used before, anything you are unsure about.
5. **The `review-gate` check** turns green once the bot has commented on the PR and, for `needs-human` PRs, someone approved. Pushes after the bot's comment do not re-trigger it.

### Commands in a PR comment

| Comment | What happens |
|---|---|
| `/review` | The bot reviews the PR again and updates its comment |
| `/review deep` | Full multi-lens `/ios-review` instead of the fast check; takes longer |

### Red zones

The shared list: money (purchases, payment SDKs, price computation), identity (login, tokens, Keychain, biometrics), user data (persistence schema and migrations, deleting data, personal data in analytics or logs), app infrastructure (request pipeline, DI container, app lifecycle, root navigation, deep links) and release (new dependencies, entitlements, privacy manifest, build settings, CI, Fastlane). A new endpoint or a new screen is not a red zone; changing what every request goes through is.

Each project adds its own zones in the `## Red zones` section of its `AGENTS.md`, one line each.

## Feature review in the app

The time saved on reading diffs goes into trying finished features in the app. This is a second iOS expert's look at the feature as a whole, not QA: QA tests acceptance criteria, the feature reviewer looks for what passes them and is still wrong on iOS.

- The author of the PR that completes a functional whole (a flow, a screen, an animation, Liquid Glass support…) adds the `feature-complete` label and a `## What to try` section. `/pull-request` asks about both.
- After the nightly or on-demand build containing the PR is uploaded, a Slack message mentions the project reviewer with the PR and its What to try text. An on-demand build with a custom changelog sends no message.
- The reviewer installs the build from TestFlight and spends 15–30 minutes on it:
    - does the feature behave as an iOS user expects, does the navigation make sense (back, swipe back, modals, deep links)
    - is a custom component used where a system one exists
    - do Dynamic Type, dark mode, the keyboard, animations and transitions work
- Findings go to the Slack thread or a Jira ticket. Edge-case input testing stays with QA.

## Setup for a project

Organisation secrets, set once by an org admin: `ANTHROPIC_API_KEY`, `CLAUDE_MARKETPLACE_TOKEN`, `SLACK_WEBHOOK_FEATURE_REVIEW`.

1. Copy `.github/workflows/ai-review.yml`, `review-gate.yml`, the updated `nightly-build.yml` and `on-demand-build.yml`, and `.github/pull_request_template.md` from the [iOS project template](https://github.com/futuredapp/iOS-project-template).
2. Set repository variables (Settings → Secrets and variables → Actions → Variables):
    - `PROJECT_REVIEWER` — GitHub login (or `org/team`) of the project reviewer, requested on `needs-human` PRs
    - `FEATURE_REVIEWER_SLACK` — Slack mention for the feature review message, e.g. `<@U0123456>`
3. In the `default branch protection` ruleset set required approvals to `0` and add the required status checks `test` and `review-gate`. The `needs-human` and `feature-complete` labels are created by the first bot run.
4. Fill in `## Red zones` in `AGENTS.md`.
