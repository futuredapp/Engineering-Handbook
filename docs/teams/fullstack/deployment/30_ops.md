# Operations & Access Management

This guide covers operational practices for managing project infrastructure, accounts, and access controls.

## Current State & Goals

!!! warning "Current State"

    Access management and operational setup is inconsistent across projects. Many projects use personal accounts for services, lack standardized email configurations, and have no formal access audit process. This section defines where we want to be.

## Service Accounts

**Never use personal accounts for project infrastructure.** All external services, cloud resources, and third-party integrations should use dedicated service accounts.

### Naming Convention

- **Service emails** — owned by Futured, not by individuals; one address per project role
- **Cloud service accounts** — scoped to a single project, follow the cloud provider's required format
- **GitHub bot** — a dedicated org-level bot account performs automated tasks (releases, dependency updates, CI commits) instead of personal credentials

The concrete alias scheme, bot username, and naming patterns live in our internal Ops runbook.

### What Needs a Service Account

| Service | Account type | Owner |
|---|---|---|
| Cloud provider (GCP/DO) | Service account with scoped IAM roles | Project owner |
| Container registry | Deploy token or service account | CI/CD pipeline |
| Sentry | Organization-level, project-scoped | Project owner |
| Email provider (Resend, etc.) | API key tied to ops email | Project owner |
| Payment provider (Stripe, etc.) | Organization account, not personal | Project owner + delivery |
| DNS / Domain registrar | Organization account | Ops specialist |

### Rules

- Service accounts use the minimum permissions needed (principle of least privilege)
- API keys and tokens are stored in the cloud provider's secret manager, never in code or personal password managers
- When a team member leaves, their personal access is revoked — service accounts remain unaffected

## Email Roles

Every project provisions standardized email roles backed by Futured-owned aliases, never personal addresses:

- **Operations / monitoring** — receives infrastructure alerts, on-call notifications, and automated reports
- **Transactional sender** — `From:` address for password resets, account notifications, etc.; not a monitored inbox
- **Support** — user-facing inbox for projects that expose external support

The concrete alias pattern is documented internally.

## Access Audit

Quarterly access audit checklist per project:

- [ ] List all service accounts and their permissions — remove unused ones
- [ ] Verify no personal accounts are used for infrastructure
- [ ] Rotate API keys and tokens that have exceeded their defined rotation window
- [ ] Review cloud IAM roles — remove overly broad permissions
- [ ] Ensure all team members have appropriate access (not too much, not too little)
- [ ] Verify GitHub repository access matches current team composition

## Container Registry & OCI

### Registry Setup

- **GCP**: Google Artifact Registry — one registry per project or shared within an environment
- **DO**: Digital Ocean Container Registry
- **GitHub**: `ghcr.io` for open-source projects

### Best Practices

- Tag images with git commit SHA for traceability (see [Deployment Process](00_deployment.md))
- Set up image retention policies — auto-delete images older than 90 days (except tagged releases)
- Scan images for vulnerabilities in CI (e.g. `trivy`, `grype`)
- Use multi-stage builds to minimize image size and attack surface
- Pin base image versions (e.g. `node:24.0-alpine`, not `node:latest`)

## Cloud Resource Organization

### GCP

```
Organization: futured.app
  └── Project: <client>-<project>-<env>
       ├── Cloud Run (compute)
       ├── Cloud SQL (database)
       ├── Secret Manager (secrets)
       └── Cloud Storage (files)
```

### Digital Ocean

```
Team: Futured
  └── Project: <client>-<project>
       ├── App Platform (compute)
       ├── Managed Database
       └── Spaces (files)
```

### Rules

- Separate cloud projects/environments for dev and production
- Use consistent naming across all resources
- Tag resources with project name and environment for cost tracking
- Set up billing alerts to catch unexpected cost spikes