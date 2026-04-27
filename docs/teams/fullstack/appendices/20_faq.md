# FAQ

Answers to common questions from new team members and recurring topics.

## Onboarding

### How do I get access to the project repositories?

Ask your team lead to add you to the [Futured GitHub organization](https://github.com/futuredapp). You'll need a GitHub account. Once added, you'll have access to all team repositories.

### How do I set up my local environment?

Follow the [Local Setup Instructions](../dev_env/00_local_setup.md). In short:

1. Install Node.js (via nvm), Docker Desktop (or OrbStack on Mac), and Git
2. Clone the repository
3. Copy `docker-compose.dist.yml` to `docker-compose.yml` and fill in the values
4. Run `docker compose up -d`
5. Run migrations and seed data

### Where do I find the environment variables?

Ask your team lead or a colleague working on the project. Credentials are shared securely (not via Slack/email). See [Secrets Management](../dev_env/10_secrets.md) for the full process.

### Which IDE should I use?

We don't mandate a specific IDE. Most team members use:

- **WebStorm** (JetBrains) — Full-featured, excellent TypeScript/Vue support
- **VS Code** — Lightweight, great with extensions
- **Cursor** — VS Code fork with built-in AI features

Whatever you use, make sure ESLint and Prettier are configured to run on save.

## Development

### REST or GraphQL — which should I use?

See the [Backend](../tech_stack/10_backend.md) page for a comparison table. In short:

- **REST** for simple CRUD, public APIs, file operations, webhooks, and scheduled jobs (external scheduler calls an API-key-protected endpoint)
- **GraphQL** for complex data relationships, flexible frontend queries, real-time subscriptions

Most projects start with REST. Use GraphQL when the frontend needs outgrow simple endpoints.

### How do I create a new feature branch?

```bash
git checkout develop
git pull origin develop
git checkout -b feature/ABC-123-short-description
```

See [Git Flow](../development/00_git_flow.md) for the full branching model.

### How do I handle database schema changes?

Always use migrations. Never modify the database schema manually. See the migration sections in [Database](../tech_stack/20_database.md) and [Deployment](../deployment/00_deployment.md).

### Why do we use Docker for local development?

Docker ensures everyone runs the same environment regardless of their OS. It eliminates "works on my machine" issues, makes onboarding faster, and keeps dependencies (PostgreSQL, Redis, etc.) isolated from your system.

### How do I add a new package?

```bash
yarn add package-name           # production dependency
yarn add -D package-name        # development dependency
```

Before adding a package, check:

- Is it actively maintained?
- Does it have TypeScript types?
- Does it add significant bundle size? (check on [bundlephobia.com](https://bundlephobia.com))
- Is there already a similar package in the project?

## Code Review

### How quickly should I review a PR?

Aim to start a review within the same business day. If you can't review in time, let the author know so they can find another reviewer.

### What should I focus on during code review?

1. **Correctness** — Does the code do what it's supposed to?
2. **Security** — Are there any vulnerabilities (SQL injection, XSS, exposed secrets)?
3. **Architecture** — Does it fit the project structure? Is it in the right module?
4. **Edge cases** — What happens with empty input, null values, concurrent requests?
5. **Tests** — Are critical paths tested?

See [Code Review Process](../development/20_code_review_process.md) for the full guidelines.

### Can I use AI tools for code review?

Yes. We use Copilot and CodeRabbit for automated analysis. They catch common issues and provide suggestions. However, AI review supplements — it doesn't replace — human review. Always verify AI suggestions against the project context.

## Deployment

### How do I deploy to the dev environment?

Merge your PR to `develop`. The CI/CD pipeline will automatically build and deploy.

### How do I deploy to production?

Create a PR from `develop` to `main`. After approval and merge, the pipeline deploys automatically. See [Deployment Process](../deployment/00_deployment.md).

### What do I do if a deployment breaks production?

Follow the [Rollback Procedures](../deployment/10_rollback.md). The quickest fix is usually redeploying the previous container image.

## Architecture Decisions

### Why NestJS instead of Express?

NestJS provides structure, dependency injection, and modularity out of the box. Express gives you freedom but requires you to build all of that yourself. For team projects, the consistency of NestJS saves more time than the flexibility of Express.

### Why Vue instead of React?

Vue was adopted as our primary frontend framework based on team experience and project needs. Vue's Composition API + TypeScript provides an excellent developer experience. For projects where React is a better fit (e.g., client requirement, React Native), we use React.

### Why PostgreSQL as the default database?

PostgreSQL offers the best balance of reliability, features, and performance for our typical use cases (transactional web applications). It handles JSON data well enough to cover most semi-structured data needs too.
