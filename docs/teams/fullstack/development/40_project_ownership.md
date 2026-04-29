# Technical Ownership & Responsibilities

This guide defines how we assign and manage technical ownership of projects within the fullstack team — who is responsible for the codebase, infrastructure, and technical health of each project.

## The Problem We're Solving

Without clear technical ownership, projects drift into "no one's responsibility" territory — dependencies go stale, CI breaks silently, infrastructure config rots, and knowledge concentrates in a single person. This section exists to prevent that.

## Tech Owner

Every project has a designated **tech owner** — a fullstack developer who is the go-to person for all technical decisions and the health of the codebase.

**Responsibilities:**

- Keeping dependencies up to date (security patches, major upgrades)
- Ensuring CI/CD pipelines are functional
- Reviewing and maintaining infrastructure configuration
- Onboarding new team members to the project codebase
- Maintaining the project README and technical documentation
- Making architecture decisions (or escalating them to the team)

**Tech ownership is assigned at project kickoff and documented in the project README.**

### Backup

Every project must have at least one **backup person** who:

- Has a working local development environment
- Understands the architecture and key business logic
- Can deploy to production independently
- Is available when the tech owner is unavailable

This directly addresses the "bus factor" — no project should depend on a single person.

## No One-Person Shows

!!! warning "Hard Rule"

    No project should have only one person who can work on it. If you are the only person who understands a project, it is your responsibility to onboard someone else.

**How to prevent one-person shows:**

- Pair programming sessions during complex feature development
- Documented architecture decisions (ADRs) in the repository
- Regular knowledge-sharing during cross-project syncs
- Rotate who handles hotfixes and deployments

## Cross-Project Syncs

The team holds regular syncs to share knowledge across projects:

- **What**: Short updates on architecture decisions, interesting problems, new patterns
- **Why**: Prevent knowledge silos, share reusable solutions, align on team-wide practices
- **Format**: Each tech owner gives a 5-minute update on anything noteworthy

## Gray Area Responsibilities

The fullstack team sometimes touches things outside its core scope — DevOps, infrastructure, email setup, third-party service configuration. These need clear boundaries:

- **If it's a one-time setup** (e.g. DNS, email config) — document it and hand off to the appropriate team or ops specialist
- **If it's ongoing maintenance** (e.g. infrastructure monitoring) — assign explicit ownership, don't let it fall between teams
- **If you're unsure who owns it** — raise it in the cross-project sync and assign it

The goal is not to own everything — it's to make sure nothing is unowned.

## Project Handover Checklist

When a project changes tech ownership or a team member leaves:

- [ ] README is up to date with setup instructions, architecture overview, and key contacts
- [ ] All credentials and access are transferred (no personal accounts)
- [ ] CI/CD pipelines are documented and functional
- [ ] The backup person has deployed to production at least once
- [ ] Open issues and technical debt are documented
- [ ] Knowledge transfer session completed (recorded if possible)

## Project Health Audits

Periodically (quarterly recommended), the tech owner reviews their project for:

- [ ] Dependencies are up to date (no critical security vulnerabilities)
- [ ] CI/CD is green and runs in a reasonable time
- [ ] Access controls use service accounts, not personal accounts
- [ ] Monitoring and alerting are functional
- [ ] Documentation reflects the current state of the project
- [ ] At least two people can work on and deploy the project