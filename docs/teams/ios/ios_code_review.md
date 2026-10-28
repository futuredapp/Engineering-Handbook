# Code Review

A PR does not need an approval from a colleague. It needs an author who understands every line, a green AI review gate, and evidence that it works. People review where their judgment matters: risky changes, and finished features in the app.

## The flow

1. **The author owns the code.** You merge only what you understand and can explain. `/ios-review` is there to help you before opening the PR: conventions, patterns and code drift (duplicates, leftovers, custom code where a system component fits).
2. **Every PR carries evidence.** Screenshots or a screen recording for UI changes, a test that fails without the fix for bug fixes, "No behaviour change." for refactors, what and why for config. `/pull-request` writes the `## Evidence` section with you.
3. **The review bot reviews every push.** When a ready PR into `develop` is opened or updated, the bot runs `/ios-pr-check` and keeps a single comment up to date: risk, whether the diff matches the description, evidence, verified serious findings and code drift. Findings never block the merge; you decide what to fix.
4. **A human looks only at red zones.** When the change touches a red zone, the bot labels the PR `needs-human`, requests the project reviewer and lists the 1–3 places to look at. Add the label yourself whenever you want a second pair of eyes: an architecture decision, an SDK you have not used before, anything you are unsure about.
5. **The `review-gate` check** is green when the bot reviewed the latest commit and found low risk, or when someone with write access other than the author approved the latest commit. High risk and the `needs-human` label always need that approval; removing the label does not lower a high-risk result. Changes to CI, agent hooks and permissions (`.claude/settings.json`, `.claude/hooks/`), dependency lockfiles, Fastlane, entitlements, the privacy manifest, `.xcconfig` files, `.gitattributes`, `.gitmodules`, build script phases and source files git shows as binary are always high risk, whatever the bot concludes.

### When the bot can't help

| Situation | What to do |
|---|---|
| The bot failed, Claude is down or the subscription's usage limit is reached | Comment `/review` to retry, or get an approval of the latest commit — it turns the gate green on its own |
| You changed the description or retargeted the PR to `develop` after the bot ran | Neither re-runs the bot. Comment `/review` to update the comment; after a retarget the gate waits for it |
| Dependabot or another bot opened the PR | The bot skips it: comment `/review`, or approve it (if the check stays pending after the approval, comment `/review`) |
| A release or hotfix PR into `main` | The gate passes automatically; the `main` ruleset decides what else is required. If the same branch also has a PR into `develop`, the check follows that PR. |

`/review` must be the whole comment, from someone with write access. The bot reads the project's rules (`AGENTS.md`, `CLAUDE.md`) from the base branch, so a PR cannot change the rules it is judged by. It can read only the repository: no shell, no network, nothing outside the checkout.

The gate protects against mistakes, not against a colleague who wants to bypass it: anyone with write access could set the status by hand, just as an admin can merge without checks today.

### Red zones

The shared list:

- **Money:** purchases, payment SDKs, price computation.
- **Identity:** login, tokens, Keychain, biometrics.
- **User data:** persistence schema and migrations, deleting data, personal data in analytics or logs.
- **App infrastructure:** request pipeline, DI container, app lifecycle, root navigation, deep links.
- **Release:** new dependencies, entitlements, privacy manifest, build settings, CI, Fastlane, agent hooks and permissions.

A new endpoint or a new screen is not a red zone; changing what every request goes through is. Each project adds its own zones in the `## Red zones` section of its `AGENTS.md`, one line each.

## Feature review in the app

The time saved on reading diffs goes into trying finished features in the app. This is a second iOS expert's look at the feature as a whole, not QA: QA tests acceptance criteria, the feature reviewer looks for what passes them and is still wrong on iOS.

- The author of the PR that completes a functional whole (a flow, a screen, an animation, Liquid Glass support…) adds the `feature-complete` label and a `## What to try` section. `/pull-request` asks about both.
- After the nightly or on-demand build containing the PR is uploaded, a Slack message mentions the project reviewer with the PR and its What to try text. An on-demand build with a custom changelog sends no message.
- The reviewer installs the build from TestFlight and spends 15–30 minutes on it:
    - does the feature behave as an iOS user expects, does the navigation make sense (back, swipe back, modals, deep links)
    - is a custom component used where a system one exists
    - do Dynamic Type, dark mode, the keyboard, animations and transitions work
- Findings go to the Slack thread or a Jira ticket. Edge-case input testing stays with QA.

## Setup

### Once per organisation (org admin)

- Org secrets, shared with the iOS repositories:
    - `CLAUDE_CODE_OAUTH_TOKEN` — the bot runs on a Claude subscription, not on API tokens. Create the token with `claude setup-token` while logged in to the subscription account the reviews should use; it is long-lived, so renew it before it expires. Reviews count against that account's usage limits. A project on another account sets a repository secret of the same name, which takes precedence; an environment secret is not used.
    - `CLAUDE_MARKETPLACE_SSH_KEY` — the private half of a read-only deploy key on `futuredapp/FTClaudeMarketplace`. It belongs to the repository, not to a person, and does not expire:
        1. `ssh-keygen -t ed25519 -N "" -C "ai-pr-review" -f marketplace-deploy-key`
        2. Add `marketplace-deploy-key.pub` in FTClaudeMarketplace → Settings → Deploy keys, without write access
        3. Store the content of `marketplace-deploy-key` as the org secret, then delete both files
    - `SLACK_WEBHOOK_FEATURE_REVIEW` — an incoming webhook for the default channel. A project that wants its own channel sets a repository secret of the same name.
- Org variable `SLACK_USER_IDS`, shared with the iOS repositories: the Slack member ID of every project reviewer, `{"github-login": "U0123456"}`. The feature review message mentions the project's `PROJECT_REVIEWER` through it; add a reviewer here once, not per project.

### New project

Everything is in the [iOS project template](https://github.com/futuredapp/iOS-project-template): `.github/workflows/code-review.yml`, the Slack webhook passed in `nightly-build.yml` and `on-demand-build.yml`, and `.github/pull_request_template.md`. Then:

1. Set the reviewer: `gh variable set PROJECT_REVIEWER --repo futuredapp/<project> --body <github-login>`. The bot requests them on `needs-human` PRs and the feature review message mentions them.
2. Set up the `default branch protection` ruleset with the command below (repository admin). It creates the ruleset, or updates an existing one and keeps its other rules: a pull request with `0` required approvals, stale approvals dismissed, no Code Owners review, and the required checks `test / test` and `review-gate`. Checks that have not run yet are fine. The `needs-human` and `feature-complete` labels are created by the first bot run.
3. Add the project's own zones to `## Red zones` in `AGENTS.md`, if it has any.

```bash
repo=futuredapp/<project>
name="default branch protection"
id=$(gh api "repos/$repo/rulesets" --jq ".[] | select(.name == \"$name\") | .id")
if [ -n "$id" ]; then rules=$(gh api "repos/$repo/rulesets/$id" --jq '.rules'); else rules='[{"type":"deletion"},{"type":"non_fast_forward"}]'; fi
# 15368 is GitHub Actions: a status of the same name from anything else does not count.
rules=$(jq -c '
  def upsert($type; f): if any(.[]; .type == $type) then map(if .type == $type then f else . end) else . + [{type: $type} | f] end;
  [{context: "test / test", integration_id: 15368}, {context: "review-gate", integration_id: 15368}] as $checks
  | upsert("pull_request"; .parameters += {required_approving_review_count: 0, dismiss_stale_reviews_on_push: true,
      require_code_owner_review: false, require_last_push_approval: false, required_review_thread_resolution: false})
  | upsert("required_status_checks"; .parameters.strict_required_status_checks_policy //= false
      | .parameters.required_status_checks = [(.parameters.required_status_checks // [])[] | select(.context as $c | $checks | all(.context != $c))] + $checks)
' <<< "$rules")
if [ -n "$id" ]; then
  jq -n --argjson rules "$rules" '{rules: $rules}' | gh api -X PUT "repos/$repo/rulesets/$id" --input - --jq '"updated \(.name)"'
else
  jq -n --argjson rules "$rules" --arg name "$name" '{name: $name, target: "branch", enforcement: "active",
    conditions: {ref_name: {include: ["~DEFAULT_BRANCH"], exclude: []}}, rules: $rules}' \
    | gh api -X POST "repos/$repo/rulesets" --input - --jq '"created \(.name)"'
fi
```

Setting it up by hand instead: pick `review-gate` without a slash in the status checks list; the entries with `/` are jobs that pass even when the PR still needs a human.

### Existing project

1. Add `.github/workflows/code-review.yml` and `.github/pull_request_template.md` from the template as they are.
2. In `nightly-build.yml` and `on-demand-build.yml`, bump the shared workflow refs to the template's version and pass `SLACK_WEBHOOK_FEATURE_REVIEW: ${{ secrets.SLACK_WEBHOOK_FEATURE_REVIEW }}` with the other secrets.
3. Remove `.github/CODEOWNERS` (it requests reviewers on every PR), then follow steps 1–3 of a new project; the ruleset command turns off the Code Owners review.
