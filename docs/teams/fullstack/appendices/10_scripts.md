# Useful Scripts & Tools

A collection of commonly used scripts and commands for day-to-day development work.

## Database Scripts

### Reset Local Database

Drops and recreates the database, runs migrations, and seeds development data:

```bash
#!/bin/bash
# scripts/db-reset.sh

echo "Dropping and recreating database..."
docker compose exec postgres psql -U myuser -c "DROP DATABASE IF EXISTS mydb;"
docker compose exec postgres psql -U myuser -c "CREATE DATABASE mydb;"

echo "Running migrations..."
docker compose exec api yarn migrate

echo "Seeding development data..."
docker compose exec api yarn seed

echo "Database reset complete."
```

### Database Dump and Restore

```bash
# Export a database dump
docker compose exec -T postgres pg_dump -U myuser mydb > backup.sql

# Import a database dump
docker compose exec -T postgres psql -U myuser mydb < backup.sql
```

### Quick Migration Commands

```bash
# Prisma
docker compose exec api yarn prisma migrate dev --name description_of_change
docker compose exec api yarn prisma generate
docker compose exec api yarn prisma studio   # Visual database browser

# TypeORM
docker compose exec api yarn typeorm migration:generate -n MigrationName
docker compose exec api yarn typeorm migration:run
docker compose exec api yarn typeorm migration:revert
```

## Docker Scripts

### Clean Rebuild

When things are broken and you want a fresh start:

```bash
# Stop everything, remove volumes, rebuild
docker compose down -v
docker compose build --no-cache
docker compose up -d
```

### View Logs

```bash
# Follow all logs
docker compose logs -f

# Follow a specific service
docker compose logs -f api

# Show last 100 lines
docker compose logs --tail=100 api
```

### Shell Access

```bash
# Open a shell in the API container
docker compose exec api sh

# Run a one-off command
docker compose exec api yarn lint
```

## Development Helpers

### Generate NestJS Resources

```bash
# Generate a complete CRUD module
yarn nest generate resource users

# Generate individual components
yarn nest generate module users
yarn nest generate service users
yarn nest generate controller users
```

### Quick Dependency Check

```bash
# Check for outdated packages
yarn outdated

# Check for security vulnerabilities
yarn audit

# Update all packages to latest within semver range
yarn upgrade
```

### TypeScript Type Checking

```bash
# Run type checker without emitting files
yarn tsc --noEmit

# Watch mode for continuous type checking
yarn tsc --noEmit --watch
```

## Git Helpers

### Clean Up Local Branches

```bash
# Delete all local branches that have been merged to develop
git branch --merged develop | grep -v "develop\|main" | xargs git branch -d

# Prune remote tracking branches that no longer exist
git remote prune origin
```

### Quick Rebase on Develop

```bash
git fetch origin
git rebase origin/develop
```

## Scripts Convention

Every project should define these scripts in `package.json`:

```json
{
  "scripts": {
    "dev": "nest start --watch",
    "build": "nest build",
    "start": "node dist/main.js",
    "lint": "eslint . --fix",
    "format": "prettier --write .",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:e2e": "jest --config jest.e2e.config.ts",
    "migrate": "prisma migrate deploy",
    "migrate:dev": "prisma migrate dev",
    "seed": "ts-node prisma/seed.ts",
    "type-check": "tsc --noEmit"
  }
}
```
