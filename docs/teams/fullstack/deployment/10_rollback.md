# Rollback Procedures

When a deployment introduces critical issues, we need to revert quickly. This guide covers rollback strategies for different scenarios.

## Quick Rollback

The fastest rollback is redeploying the previous known-good revision. Both GCP and Digital Ocean keep a history of deployed revisions, making this straightforward.

### Via Platform Admin Console (Most Common)

This is the approach we use most often — no CLI needed.

=== "GCP Cloud Run"

    1. Open the [Cloud Run console](https://console.cloud.google.com/run)
    2. Select the affected service
    3. Go to the **Revisions** tab
    4. Find the previous healthy revision
    5. Click **Manage Traffic** and route 100% traffic to the previous revision

=== "Digital Ocean App Platform"

    1. Open the [Apps dashboard](https://cloud.digitalocean.com/apps)
    2. Select the affected app
    3. Go to the **Activity** tab
    4. Find the last successful deployment
    5. Click **Rollback to this deployment**

### Via GitHub Actions

Alternatively, you can rerun the last successful deployment workflow from the GitHub Actions tab. This rebuilds and redeploys the previous version.

### Via Git (New Deployment)

If the platform UI is not available or you need a code-level revert:

1. `git revert` the problematic commit(s) on the target branch
2. Push — the CI/CD pipeline will deploy the reverted code automatically

## Database Rollback

Database rollbacks are more complex and depend on the nature of the migration.

### Reversible Migrations

If the migration tool supports down migrations, you can revert the last migration. Always verify what the down migration does before running it.

### Non-reversible Migrations

If the migration added data, changed types, or dropped columns:

1. **Do NOT** run a down migration blindly
2. Assess the impact — can the previous code work with the new schema?
3. If yes, just roll back the application and leave the database as-is
4. If no, create a new forward migration that undoes the change safely
5. For data loss scenarios, restore from backup (see below)

### Restoring from Backup

As a last resort, restore the database from a point-in-time backup. Both GCP Cloud SQL and Digital Ocean Managed Databases support this through their admin consoles:

- **GCP**: Cloud SQL → Instance → Backups → Restore
- **Digital Ocean**: Databases → Select DB → Backups → Restore

!!! warning "Data Loss"
    Restoring a backup will lose all data written after the backup was taken. Only use this as a last resort and communicate with the team before proceeding.

## Rollback Decision Tree

```
Issue detected after deployment
        │
        ├── Application error (code bug)?
        │       → Roll back to previous revision in platform console
        │
        ├── Migration broke the schema?
        │       ├── Previous code works with new schema?
        │       │       → Roll back application only (leave DB as-is)
        │       └── Previous code doesn't work with new schema?
        │               → Create corrective migration + redeploy
        │
        └── Data corruption?
                → Restore from backup + roll back application
```

## Incident Response Steps

When a production issue is detected:

1. **Acknowledge** — Notify the team in Slack that you're investigating
2. **Assess severity** — Is the app down? Is data being corrupted? Is it a visual bug?
3. **Decide on rollback** — Use the decision tree above
4. **Execute rollback** — Roll back to previous revision and/or fix the database
5. **Verify** — Confirm the rollback resolved the issue
6. **Communicate** — Update the team and affected stakeholders
7. **Post-mortem** — Document what happened, why, and how to prevent it

## Prevention

- Always test migrations against a copy of production data before deploying
- Use backward-compatible migrations (see [Deployment Process](00_deployment.md))
- Deploy during low-traffic periods for high-risk changes
- Keep deployments small and frequent — smaller changes are easier to roll back
