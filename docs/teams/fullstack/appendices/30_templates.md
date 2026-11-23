# Templates

Reusable templates for common documents and processes.

## Pull Request Template

Save as `.github/pull_request_template.md` in your repository:

```markdown
## Summary

<!-- What does this PR do and why? Reference the Jira ticket. -->

## Evidence
<!-- UI change: screenshots or a screen recording. Endpoint: a test or a request/response sample. Fix: the test that fails without it. Migration: what it does to existing rows. Refactor: "No behaviour change." Config or infrastructure: what changed and why. -->

## What to try
<!-- Optional: 1-3 lines telling a reviewer where to go and what to look at. Delete otherwise. -->
```

## Project README Template

```markdown
# Project Name

Brief description of what this project does.

## Tech Stack

- **Backend**: NestJS, TypeScript, PostgreSQL
- **Frontend**: Vue 3 / Nuxt 4, TypeScript
- **Infrastructure**: Docker, GitHub Actions, GCP / Digital Ocean

## Quick Start

### Prerequisites

- Node.js (see `.nvmrc`)
- Docker Desktop or OrbStack

### Setup

1. Clone the repository
2. `cp docker-compose.dist.yml docker-compose.yml`
3. Fill in environment variables (ask a team member for values)
4. `docker compose up -d`
5. `docker compose exec api yarn migrate`
6. `docker compose exec api yarn seed`

### Development

- **API**: http://localhost:8080
- **Frontend**: http://localhost:3000
- **API docs**: http://localhost:8080/api/docs

### Scripts

| Script | Description |
|---|---|
| `yarn dev` | Start dev server with hot reload |
| `yarn build` | Build for production |
| `yarn test` | Run tests |
| `yarn lint` | Lint and format |

## Environment Variables

| Variable | Description | Required |
|---|---|---|
| `DATABASE_URL` | PostgreSQL connection string | Yes |
| `JWT_SECRET` | Secret for signing tokens | Yes |

## Deployment

Deployed via GitHub Actions. See `.github/workflows/` for pipeline configuration.

- **Dev**: Deploys on merge to `develop`
- **Production**: Deploys on merge to `main`
```

## Docker Compose Template

Starting point for new project infrastructure:

```yaml
# docker-compose.dist.yml
services:
  api:
    build:
      context: .
      dockerfile: dev.Dockerfile
    ports:
      - "8080:8080"
    volumes:
      - ./src:/app/src
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://myapp:localdev@postgres:5432/myapp
      JWT_SECRET: local-dev-secret-change-in-production
      REDIS_HOST: cache
      REDIS_PORT: 6379
    depends_on:
      - postgres
      - cache

  postgres:
    image: postgres:18-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: myapp
      POSTGRES_PASSWORD: localdev
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  cache:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  pgdata:
```
