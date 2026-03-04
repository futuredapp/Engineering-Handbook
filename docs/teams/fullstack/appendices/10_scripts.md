# Useful Scripts & Tools

A collection of commonly used scripts and commands for day-to-day development work.

## Database Scripts

### Reset Local Database

Drops and recreates the database, runs migrations, and seeds development data:

```bash
#!/bin/bash
# scripts/db-reset.sh

echo "Dropping and recreating database..."
docker compose exec postgres psql -U $POSTGRES_USER -c "DROP DATABASE IF EXISTS $POSTGRES_DB;"
docker compose exec postgres psql -U $POSTGRES_USER -c "CREATE DATABASE $POSTGRES_DB;"

echo "Running migrations..."
docker compose exec api npm run migrate

echo "Seeding development data..."
docker compose exec api npm run seed

echo "Database reset complete."
```

### Database Dump and Restore

```bash
# Export a database dump
docker compose exec postgres pg_dump -U myuser mydb > backup.sql

# Import a database dump
docker compose exec -T postgres psql -U myuser mydb < backup.sql
```

### Quick Migration Commands

```bash
# Prisma
docker compose exec api npx prisma migrate dev --name description_of_change
docker compose exec api npx prisma generate
docker compose exec api npx prisma studio   # Visual database browser

# TypeORM
docker compose exec api npx typeorm migration:generate -n MigrationName
docker compose exec api npx typeorm migration:run
docker compose exec api npx typeorm migration:revert
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
docker compose exec api npm run lint
```

## Development Helpers

### Generate NestJS Resources

```bash
# Generate a complete CRUD module
npx nest generate resource users

# Generate individual components
npx nest generate module users
npx nest generate service users
npx nest generate controller users
```

### Quick Dependency Check

```bash
# Check for outdated packages
npm outdated

# Check for security vulnerabilities
npm audit

# Update all packages to latest within semver range
npm update
```

### TypeScript Type Checking

```bash
# Run type checker without emitting files
npx tsc --noEmit

# Watch mode for continuous type checking
npx tsc --noEmit --watch
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

## npm Scripts Convention

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
