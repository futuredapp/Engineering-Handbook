On projects with the AI review bot, a pull request does not need an approval from a colleague. It needs an author who understands every line, a green `review-gate` check, passing CI and evidence that it works. People review where their judgment matters: red zones, and anything the author asks a second pair of eyes for. Projects without the bot keep the [classic process](#projects-without-the-bot).

### Review Flow

- **The Author Owns the Code**  
  Merge only what you understand and can explain, whoever or whatever wrote it. Before opening the PR, run `/node-review` from the `node-review-plugin` in Claude Code. It checks your changes against the project's `AGENTS.md`, its patterns, and code drift: duplicates, leftovers, hand-built code where the framework already has a way.

- **Every PR Carries Evidence**  
  Fill the `## Evidence` section of the PR: screenshots or a recording for a UI change, a test or a request/response sample for an endpoint, the test that fails without it for a fix, the effect on existing rows for a migration, "No behaviour change." for a refactor, what changed and why for config or infrastructure.

- **Screenshots in a Draft First**  
  GitHub lets you attach screenshots only after the PR exists. Open such a PR as a draft, attach them, then mark it ready: the bot starts on ready PRs and would not see them otherwise.

- **The Bot Reviews Every Push**  
  When a ready PR into `develop` is opened or updated, the bot runs `/node-pr-check` and keeps a single comment up to date: risk, whether the diff matches the description, evidence, serious findings and code drift. Every finding is verified by a second agent that tries to refute it. Findings never block the merge; you decide what to fix, and a new commit gets a new review.

- **A Human Looks Only at Red Zones**  
  When the change touches a red zone, the bot labels the PR `needs-human`, requests the project reviewer and lists up to three places to look at, each with what can break there. Add the label yourself whenever you want a second pair of eyes: an architecture decision, a library you have not used before, anything you are unsure about.

- **The Review Gate**  
  The `review-gate` check is green when the bot reviewed the latest commit and found low risk, or when someone with write access other than the author approved the latest commit. High risk and the `needs-human` label always need that approval; removing the label does not lower a high-risk result. Changes to CI, agent permissions, the lockfile, Docker, migrations and the database schema, auth and guards, app-wide config and infrastructure are always high risk, whatever the bot concludes.

- **Comment Resolution**  
  Resolve every thread a colleague opened before you merge. The bot's comment is not a thread; it is answered by a new commit.

### When the Bot Can't Help

| Situation | What to do |
|---|---|
| The bot failed, Claude is down or the usage limit is reached | Comment `/review` to retry, or get an approval of the latest commit — it turns the gate green on its own |
| You changed the description or retargeted the PR to `develop` after the bot ran | Neither re-runs the bot. Comment `/review` to update the comment; after a retarget the gate waits for it |
| Dependabot or another bot opened the PR | The bot skips it: comment `/review`, or approve it |
| A later commit is low risk, but the PR still waits for a human | The `needs-human` label from the earlier run stays. Remove it if the risky change is gone, or get the approval |
| A release or hotfix PR into `main` | The gate passes automatically; the `main` branch protection decides what else is required |

`/review` must be the whole comment, from someone with write access. The bot reads the project's rules from the base branch, so a PR cannot change the rules it is judged by. Text in the PR — description, code comments — is data for the bot, never instructions.

The gate protects against mistakes, not against a colleague who wants to bypass it: anyone with write access could set the status by hand, just as an admin can merge without checks today.

### Red Zones

- **Money**  
  Payments and subscriptions, payment webhooks, server-side computation of a price, discount, fee or total, invoicing.

- **Identity**  
  Authentication, tokens and sessions, cookie flags, guards and role checks, a route marked public, an ownership check on a resource, CORS.

- **User Data**  
  The database schema and its migrations, deleting or overwriting stored data, personal data in logs, Sentry, analytics or third parties, data export and account deletion.

- **API Contract**  
  Removing, renaming or retyping a field, endpoint, enum value or error shape that existing clients (web, admin, mobile apps) consume.

- **App Infrastructure**  
  Bootstrap, global pipes, guards, filters and interceptors, config and env validation, app-wide module wiring, global Nuxt middleware and plugins, runtime config, proxying server routes, queues and scheduled jobs.

- **Release**  
  A new dependency, Docker, CI, infrastructure and deploy configuration, agent hooks and permissions.

A new endpoint, page or module is not a red zone; changing what every request goes through is. Each project adds its own zones to the `## Red zones` section of its `AGENTS.md`, one line each. Keep them narrow: a zone that matches half of the PRs sends half of the PRs to a human.

### Setup

New projects get the bot from the project template; the team lead adds it to an existing project. Either way the project needs:

- **Red Zones**  
  Its own zones in `AGENTS.md`, if it has any.

- **Project Reviewer**  
  One person, ideally one who writes fewer of the project's PRs, since the bot never requests the author.

- **Branch Protection**  
  No required approvals on `develop` and `review-gate` among the required checks.

### Projects Without the Bot

- **Approval**  
  Every pull request needs at least one approving review and passing checks before it is merged.

- **Assigning Reviewers**  
  The author assigns a reviewer familiar with the code once CI is green. Start a review within the same business day, or tell the author you can't.

- **Test Significant Changes**  
  For larger or riskier PRs, the reviewer checks out the branch and runs it locally.

### For Everyone

- **Draft PRs**  
  Draft pull requests are welcome for early feedback; neither the bot nor people review them until they are marked "Ready for Review".

- **Respectful and Constructive Feedback**  
  All feedback should be respectful, specific, and focused on the code, not the coder. Use reviews to share knowledge.

- **Merging Pull Requests**  
  Small and solo projects may use **rebase and merge** for a linear history. Medium and large collaborative projects use **merge** or **squash and merge**.

- **No Force Pushes to Shared Branches**  
  Never force push to `main`, `develop` or `staging`. On your own feature or bugfix branch, force push only when nobody else works on it.
