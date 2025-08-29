# Local Development Setup

This guide walks you through setting up your local development environment for full-stack projects. We use Docker Compose for consistent, isolated development environments.

## Prerequisites

Before starting, ensure you have the following installed:

### Required Software

- **Git** (latest version)
- **Node.js** (version specified in project's `.nvmrc`, `Dockerfile` or `package.json`)
- **Docker Desktop or Orbstack(mac only)** (latest stable version)

### Version Management

We recommend using Node Version Manager (nvm) for Node.js:

```bash
# Install nvm (if not already installed)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Restart your terminal or run:
source ~/.bashrc

# Install and use the project's Node.js version
nvm install
nvm use
```

---

## Project Setup

### 1. Clone and Checkout Latest Development Branch

```bash
# Clone the repository
git clone <repository-url>
cd <project-name>

# Fetch latest changes
git fetch origin

# Checkout the development branch
git checkout dev
git pull origin dev

```

### 2. Environment Configuration

```bash
# Copy the distribution template
cp docker-compose.dist.yml docker-compose.yml

# Edit with your local values
nano docker-compose.yml
```

**Important:** Never commit `docker-compose.yml` with actual secrets to version control.

### 4. Request Environment Variables

If you don't have the required environment variables:

#### From Colleagues
- Ask team members for a copy of their `docker-compose.yml` (with sensitive data redacted)
- Request access to shared password managers or secret stores
- Check team documentation for development credentials

#### From Deployment Configuration
- Review production/staging environment variables (if you have access)
- Use development/test API keys and credentials
- Check CI/CD pipeline configuration for environment setup

#### Working Alone
- Use placeholder values for local development
- Set up mock services where possible
- Document what needs to be configured for production

---

## Starting the Development Environment

### 1. Build and Start Services

```bash
# Build all services (first time or after changes)
docker-compose build

# Start all services in detached mode
docker-compose up -d

# Check service status
docker-compose ps
```

### 2. Verify Services Are Running

```bash
# Check all containers are healthy
docker-compose ps

# View logs for any issues
docker-compose logs api
docker-compose logs postgres
docker-compose logs cache
```

### 3. Access Your Application

- **API**: http://localhost:8080
- **Frontend**: http://localhost:3000 (if applicable)
- **Database**: localhost:5432
- **Redis**: localhost:6379

---

## Database Management

### 1. Run Migrations

The correct migration command is always specified in the project's `package.json` and `README.md`. Common examples:

```bash
# Run migrations inside the API container
docker-compose exec api npm run migrate

# Or if using a specific migration tool
docker-compose exec api npx prisma migrate dev
docker-compose exec api npx typeorm migration:run
```

**Always check the project's documentation for the correct command.**

### 2. Seed Development Data

The correct seed command is always specified in the project's `package.json` and `README.md`. Common examples:

```bash
# Run seed scripts
docker-compose exec api npm run seed

# Or manually seed if needed
docker-compose exec api npm run seed:dev
```

**Always check the project's documentation for the correct command.**

### 3. Database Access

We recommend using GUI tools for database access:

- **DataGrip** (JetBrains) - Professional database IDE
- **DBeaver** - Free, universal database tool
- **pgAdmin** - PostgreSQL-specific administration tool

Connect using these credentials (from your `docker-compose.yml`):
- **Host**: localhost
- **Port**: 5432
- **Database**: (check your docker-compose.yml)
- **Username**: (check your docker-compose.yml)
- **Password**: (check your docker-compose.yml)

---

## Development Workflow

### 1. Code Changes

```bash
# Make your changes in the mounted source directories
# Changes are automatically reflected due to volume mounting

# Check file changes
git status

# Create a feature branch
git checkout -b feature/your-feature-name
```

### 2. Testing Your Changes

```bash
# Run tests
docker-compose exec api npm test

# Run linting
docker-compose exec api npm run lint

# Run type checking
docker-compose exec api npm run type-check
```

### 3. Hot Reloading

The development environment includes hot reloading:
- API changes trigger automatic restarts
- Frontend changes are reflected immediately
- Database changes require manual migration runs

---

## Troubleshooting

### Common Issues

#### 1. Port Conflicts
```bash
# Check what's using the port
lsof -i :8080

# Stop conflicting services
sudo systemctl stop conflicting-service

# Or change ports in docker-compose.yml
```

#### 2. Docker Issues
```bash
# Clean up Docker resources
docker system prune -f

# Rebuild without cache
docker-compose build --no-cache

# Restart Docker Desktop (if on macOS/Windows)
```

#### 3. Database Connection Issues
```bash
# Check if database is running
docker-compose ps postgres

# View database logs
docker-compose logs postgres

# Reset database container
docker-compose restart postgres
```

#### 4. Environment Variable Problems
```bash
# Verify environment variables are loaded
docker-compose exec api env | grep DATABASE_URL

# Check docker-compose.yml syntax
docker-compose config
```

### Performance Issues

#### 1. Slow Builds
```bash
# Use build cache
docker-compose build --parallel

# Optimize Dockerfile with multi-stage builds
# Use .dockerignore to exclude unnecessary files
```

#### 2. Volume Mounting Performance
```bash
# On macOS/Windows, ensure file sharing is enabled
# Consider using delegated mode for better performance
volumes:
  - ./src:/app/src:delegated
```

---

## Project-Specific Setup

### Check README.md

Always check the project's README.md for:
- Project-specific setup instructions
- Additional dependencies
- Special configuration requirements
- Known issues and workarounds

### Environment Differences

Different projects may require:
- Different Node.js versions
- Additional services (Redis, MongoDB, etc.)
- Specific environment variables
- Custom build processes

---

## Best Practices

### 1. Git Workflow
- Always work from the latest `dev` branch
- Pull latest changes regularly

### 2. Docker Usage
- Use Docker for all local development
- Don't install project dependencies globally

### 3. Database Management
- Always run migrations before starting development
- Use seed data for consistent development environment
- Backup important data before destructive operations
- Use database migrations for schema changes

### 4. Troubleshooting
- Check logs first: `docker-compose logs <service>`
- Verify environment variables are set correctly
- Ensure all services are running: `docker-compose ps`
- Check for port conflicts
- Restart services when in doubt: `docker-compose restart`
