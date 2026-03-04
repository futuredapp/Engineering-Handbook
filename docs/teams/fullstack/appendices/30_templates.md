# Templates

Reusable templates for common documents and processes.

## Pull Request Template

Save as `.github/pull_request_template.md` in your repository:

```markdown
## Description

<!-- What does this PR do? Reference the Jira ticket. -->

[ABC-123](https://futured.atlassian.net/browse/ABC-123)

## Changes

-

## How to Test

<!-- Steps for the reviewer to verify this PR. -->

1.

## Checklist

- [ ] Self-reviewed on GitHub
- [ ] CI checks pass
- [ ] Tests added/updated for new functionality
- [ ] No secrets or credentials committed
```

## Project README Template

```markdown
# Project Name

Brief description of what this project does.

## Tech Stack

- **Backend**: NestJS, TypeScript, PostgreSQL
- **Frontend**: Vue 3 / Nuxt 3, TypeScript
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
5. `docker compose exec api npm run migrate`
6. `docker compose exec api npm run seed`

### Development

- **API**: http://localhost:8080
- **Frontend**: http://localhost:3000
- **API docs**: http://localhost:8080/api/docs

### Scripts

| Script | Description |
|---|---|
| `npm run dev` | Start dev server with hot reload |
| `npm run build` | Build for production |
| `npm run test` | Run tests |
| `npm run lint` | Lint and format |

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
    image: postgres:16-alpine
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
