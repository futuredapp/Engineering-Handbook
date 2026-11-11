On projects with the AI review bot, a PR does not need an approval from a colleague. It needs an author who understands every line, a green `review-gate` check, green CI and evidence that it works. People review where their judgment matters: red zones, and anything the author asks a second pair of eyes for.

Projects without the bot keep the [classic process](#projects-without-the-bot) below.

## The flow

1. **The author owns the code.** You merge only what you understand and can explain, whoever or whatever wrote it. Before opening the PR, run `/node-review` from the `node-review-plugin` in Claude Code: it checks your changes against the project's `AGENTS.md`, its patterns, and code drift (duplicates, leftovers, hand-built code where the framework already has a way).
2. **Every PR carries evidence** in its `## Evidence` section:
    - UI change: screenshots or a screen recording
    - endpoint: a test or a request/response sample
    - fix: the test that fails without it
    - migration: what it does to existing rows
    - refactor: "No behaviour change."
    - config or infrastructure: what changed and why

    Screenshots can only be attached on GitHub after the PR exists, so open such a PR as a draft, attach them, then mark it ready: the bot starts on ready PRs and would not see them otherwise.
3. **The review bot reviews every push.** When a ready PR into `develop` is opened or updated, the bot runs `/node-pr-check` and keeps a single comment up to date: risk, whether the diff matches the description, evidence, serious findings (each one verified by a second agent that tries to refute it) and code drift. Each place in the comment links to its lines. Findings never block the merge; you decide what to fix, and a new commit gets a new review.
4. **A human looks only at red zones.** When the change touches a red zone, the bot labels the PR `needs-human`, requests the project reviewer and lists up to three places to look at, each with what can break there. Add the label yourself whenever you want a second pair of eyes: an architecture decision, a library you have not used before, anything you are unsure about.
5. **The `review-gate` check** is green when the bot reviewed the latest commit and found low risk, or when someone with write access other than the author approved the latest commit. High risk and the `needs-human` label always need that approval; removing the label does not lower a high-risk result. Some paths are always high risk, whatever the bot concludes: CI and git hooks, agent hooks and permissions, the lockfile, Dockerfiles and docker-compose, migrations and the database schema, auth, guards and the decorators that open or restrict routes, app-wide config and module wiring, and infrastructure.

Human comments still count: resolve every thread a colleague opened before you merge. The bot's comment is not a thread; it is answered by a new commit.

### When the bot can't help

| Situation | What to do |
|---|---|
| The bot failed, Claude is down or the subscription's usage limit is reached | Comment `/review` to retry, or get an approval of the latest commit — it turns the gate green on its own |
| You changed the description or retargeted the PR to `develop` after the bot ran | Neither re-runs the bot. Comment `/review` to update the comment; after a retarget the gate waits for it |
| Dependabot or another bot opened the PR | The bot skips it: comment `/review`, or approve it (if the check stays pending after the approval, comment `/review`) |
| A later commit is low risk, but the PR still waits for a human | The `needs-human` label from the earlier run stays. Remove it if the risky change is gone, or get the approval |
| A release or hotfix PR into `main` | The gate passes automatically; the `main` branch protection decides what else is required |

`/review` must be the whole comment, from someone with write access. The bot reads the project's rules (`AGENTS.md`, `CLAUDE.md`) from the base branch, so a PR cannot change the rules it is judged by. It can read only the repository: no shell, no network, nothing outside the checkout. Text in the PR — description, code comments — is data for it, never instructions.

The gate protects against mistakes, not against a colleague who wants to bypass it: anyone with write access could set the status by hand, just as an admin can merge without checks today.

### Red zones

The shared list:

- **Money:** payments and subscriptions, payment webhooks, server-side computation of a price, discount, fee or total, invoicing.
- **Identity:** authentication, tokens and sessions, cookie flags, guards and role checks, a route marked public, an ownership check on a resource, CORS.
- **User data:** the database schema and its migrations, deleting or overwriting stored data, personal data in logs, Sentry, analytics or third parties, data export and account deletion.
- **API contract:** removing, renaming or retyping a field, endpoint, enum value or error shape that existing clients (web, admin, mobile apps) consume.
- **App infrastructure:** `main.ts`, global pipes, guards, filters and interceptors, config and env validation, app-wide module wiring, global Nuxt middleware and plugins, `runtimeConfig`, proxying server routes, queues and scheduled jobs.
- **Release:** a new dependency, Dockerfile or docker-compose, CI, Pulumi and deploy configuration, agent hooks and permissions.

A new endpoint, page or module is not a red zone; changing what every request goes through is. Each project adds its own zones in the `## Red zones` section of its `AGENTS.md`, one line each — for example a domain rule the code must never break, with the module it lives in. Keep them narrow: a zone that matches half of the PRs sends half of the PRs to a human.

## Setup

New projects get the bot from the project template; the team lead adds it to an existing project. Either way the project needs:

- its own red zones in `AGENTS.md`, if it has any
- a project reviewer: one person, ideally one who writes fewer of the project's PRs, since the bot never requests the author
- branch protection on `develop` with no required approvals and `review-gate` among the required checks

## Projects without the bot

Until a project has the bot, the classic rules apply:

- Every PR needs at least one approving review and passing checks before it is merged.
- The author assigns a reviewer familiar with the code once CI is green. Start a review within the same business day, or tell the author you can't.
- For larger or riskier PRs, the reviewer checks out the branch and runs it locally.

## For everyone

- **Draft PRs** are welcome for early feedback; neither the bot nor people review them until they are ready.
- **Feedback** is respectful, specific and about the code, not the coder. Use reviews to share knowledge.
- **Merging:** small and solo projects may use rebase and merge for a linear history; larger collaborative projects use merge or squash and merge.
- **No force pushes** to shared branches (`main`, `develop`, `staging`). On your own feature or bugfix branch, force push only when nobody else works on it.
