# Environment Variables & Secrets Management

Proper secrets management is critical for security and compliance. This guide covers how to handle sensitive configuration data across development and production environments.

## Core Principles

- **Never commit secrets to version control**
- **Use environment-specific configuration**
- **Implement least-privilege access**
- **Rotate secrets regularly**

---

## Local Development

### Environment Configuration Files

We use Docker Compose with environment variables for local development. The configuration is structured as follows:

#### `docker-compose.yml` (Template)
```yaml
version: '3.7'

services:
  api:
    build:
      context: ./api
      dockerfile: dev.Dockerfile
    environment:
      # External API Keys
      API_KEY_1: your-api-key
      API_KEY_2: your-api-key-2
      
      # Authentication
      OAUTH_KEY: your-oauth-key
      CLIENT_ID: your-client-id
      
      # Database
      DATABASE_URL: connection-string
      
      # Redis
      REDIS_HOST: cache
      REDIS_PORT: 6379
      
      # Environment
      NODE_ENV: development
      LOG_LEVEL: debug
```

### Git Configuration

**Never commit these files:**
- `docker-compose.yml` (with actual secrets)
- `.env`
- `.env.local`
- `.env.production`
- Any file containing actual secret values

**Always commit these files:**
- `docker-compose.dist.yml` - Template showing required variables
- `.env.example` - Template showing required variables
- `.env.dist` - Distribution template (same as .env.example)

### .gitignore Configuration

```gitignore
# Environment files
.env
.env.local
.env.production
.env.staging

# Docker Compose with secrets
docker-compose.yml

# Keep templates
!.env.example
!.env.dist
!docker-compose.dist.yml
```

---

## Project Setup Instructions

### 1. Initial Setup

When setting up a new project:

```bash
# Copy the docker-compose template
cp docker-compose.dist.yml docker-compose.yml

# Edit with your actual values
nano docker-compose.yml

# Start the application with Docker Compose
docker-compose up -d
```

### 2. README Documentation

Include a section in your README.md:

```markdown
## Environment Setup

1. Copy `docker-compose.dist.yml` to `docker-compose.yml`
2. Fill in your local development values
3. Never commit `docker-compose.yml` to version control

### Required Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@postgres/db` |
| `_API_KEY` | API key | `your-api-key` |
| `OAUTH_KEY` | OAuth authentication key | `your-oauth-key` |
| `CLIENT_ID` | Client ID for authentication | `your-client-id` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `LOG_LEVEL` | Logging verbosity | `debug` |
| `NODE_ENV` | Environment mode | `development` |
```

---

## Production Deployment

### Google Cloud Platform (GCP)

#### Secret Manager
```bash
# Create secrets via CLI
gcloud secrets create DATABASE_URL --data-file=./database-url.txt
gcloud secrets create JWT_SECRET --data-file=./jwt-secret.txt

# Grant access to service account
gcloud secrets add-iam-policy-binding DATABASE_URL \
    --member="serviceAccount:my-app@my-project.iam.gserviceaccount.com" \
    --role="roles/secretmanager.secretAccessor"
```

**Note:** You can also create and manage secrets through the Google Cloud Console UI at https://console.cloud.google.com/security/secret-manager

#### Cloud Run / App Engine
```yaml
# app.yaml
env_variables:
  NODE_ENV: production
  # Other non-sensitive variables

# Sensitive variables are injected via Secret Manager
```

### Digital Ocean

#### App Platform Environment Variables
```yaml
# .do/app.yaml
name: my-app
services:
  - name: web
    environment_slug: node-js
    envs:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        scope: RUN_AND_BUILD_TIME
        type: SECRET
        value: ${DATABASE_URL_SECRET}
```


---

## Security Best Practices

### 1. Secret Rotation

- **JWT Secrets**: Rotate every 90 days
- **API Keys**: Rotate every 6 months
- **Database Passwords**: Rotate every 12 months
- **Service Account Keys**: Rotate every 90 days

### 2. Access Control

```bash
# Example: GCP IAM roles for secret access
gcloud projects add-iam-policy-binding my-project \
    --member="serviceAccount:app@my-project.iam.gserviceaccount.com" \
    --role="roles/secretmanager.secretAccessor"
```

---

## Common Patterns

### 1. Configuration Validation

```typescript
// config/validation.ts
import { z } from 'zod';

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum(['development', 'production', 'test']),
});

export const validateEnv = () => {
  const result = envSchema.safeParse(process.env);
  if (!result.success) {
    console.error('❌ Invalid environment variables:', result.error.flatten().fieldErrors);
    process.exit(1);
  }
  return result.data;
};
```

### 2. Development vs Production

```typescript
// Example: Different database URLs
const getDatabaseUrl = () => {
  if (process.env.NODE_ENV === 'production') {
    return process.env.DATABASE_URL;
  }
  // Development: Use local database
  return process.env.DATABASE_URL || 'postgresql://localhost:5432/dev_db';
};
```