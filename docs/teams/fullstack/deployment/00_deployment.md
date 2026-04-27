# Deployment Process

This guide covers our CI/CD approach and deployment workflow for promoting code from development to production.

## Pipeline Overview

Every project uses GitHub Actions for CI/CD. The specific workflow configuration varies per project, but the general flow is the same:

```
PR opened → Lint + Test + Build → Review → Merge to develop → Deploy to dev
                                                      ↓
                                          Merge to main → Deploy to production
```

## CI — Continuous Integration

CI runs automatically on every pull request. The goal is to catch issues before code is merged.

**Every CI pipeline should include:**

1. **Install dependencies** — `yarn install --frozen-lockfile`
2. **Lint** — Run ESLint + Prettier checks
3. **Type check** — `tsc --noEmit` (or `vue-tsc --noEmit` for Vue projects). Modern build tools (esbuild, swc, Vite) skip type checking entirely, so this step is often the only place types get verified.
4. **Test** — Run unit and integration tests
5. **Build** — Verify the project compiles successfully

Some projects also run security audits (`yarn audit`) as part of CI.

If the project has integration tests that need a database, the workflow should spin up a PostgreSQL (or other) service container for the test run.

## CD — Continuous Deployment

Deployment is triggered automatically when code is merged into a target branch. The exact deployment steps depend on the hosting platform (GCP, Digital Ocean, etc.), but the general process is:

1. **Build a Docker image** from the merged code
2. **Tag the image** with the git commit SHA for traceability
3. **Push the image** to the project's container registry
4. **Deploy** to the target environment using the platform's CLI or API

Each project defines its own deployment workflow based on its infrastructure. Workflows are stored in `.github/workflows/` and should be reviewed like any other code change.

## Environment Promotion

Code flows through environments in a strict order:

| Branch | Environment | URL pattern | Auto-deploy |
|---|---|---|---|
| `develop` | Development | `dev.project.example.com` | Yes, on merge |
| `staging` | Staging | `staging.project.example.com` | Manual or on merge |
| `main` | Production | `project.example.com` | Yes, on merge |

### Pre-deployment Checklist

Before merging to `main` (production):

- [ ] All CI checks pass
- [ ] PR has been reviewed and approved
- [ ] Changes have been tested on the dev environment
- [ ] Database migrations are backward-compatible (see below)
- [ ] No breaking API changes without versioning

## Docker Images

### Tagging

We tag Docker images with the git commit SHA so every deployed version is traceable back to a specific commit. For production releases, we may also add a semantic version tag (e.g., `v1.2.3`).

### Container Registry

The registry depends on the project's hosting platform:

- **GCP projects**: Google Artifact Registry
- **DO projects**: Digital Ocean Container Registry
- **GitHub**: GitHub Container Registry (ghcr.io) for open-source projects

## Database Migrations

Migrations run as part of the deployment process, before the new application version starts serving traffic.

### Deployment Order

1. **Run migrations** — apply schema changes
2. **Start new containers** — deploy the updated application
3. **Health check** — verify the new version is responding
4. **Route traffic** — switch traffic to the new version

### Backward-Compatible Migrations

To enable zero-downtime deployments, migrations must be backward-compatible with the currently running code:

**Do:**

- Add new columns as nullable or with defaults
- Create new tables
- Add new indexes

**Don't:**

- Drop columns that the current version still reads
- Rename columns in a single step (use a two-step migration instead)
- Add non-nullable columns without defaults

## Secrets Management

Environment variables and secrets are configured per environment:

- **GCP**: Google Secret Manager
- **Digital Ocean**: App Platform environment variables (encrypted)
- **GitHub Actions**: Repository / environment secrets

See [Secrets Management](../dev_env/10_secrets.md) for details.

## Post-deployment

After every production deployment:

1. Verify the application is healthy (check monitoring dashboard)
2. Smoke test critical paths (login, core features)
3. Monitor error rates in Sentry for the first 15-30 minutes
4. If issues are detected, follow the [Rollback Procedures](10_rollback.md)
