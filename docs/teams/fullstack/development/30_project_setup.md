# Project Setup

This guide walks you through setting up a new project in the Futured Github organization, including repository creation, template usage, and initial configuration.

## 1. GitHub Repository Setup

### Create Repository in Futured Organization

1. **Navigate to Futured Organization**
   - Go to [https://github.com/futuredapp](https://github.com/futuredapp)
   - Click "New repository"

2. **Repository Configuration**
   ```
   Repository name: your-project-name
   Description: Brief description of the project
   Visibility: Private
   Initialize with: Don't initialize (we'll use templates)
   ```

### Branch Protection Rules

Set up branch protection for the `main` and `dev` branches:

#### Main Branch Protection
```
Branch name pattern: main
Require a pull request before merging: ✓
Require approvals: 1
Dismiss stale PR approvals when new commits are pushed: ✓
Require review from code owners: ✓
Require status checks to pass before merging: ✓
Require branches to be up to date before merging: ✓
Restrict pushes that create files larger than 100 MB: ✓
```

#### Dev Branch Protection
```
Branch name pattern: dev
Require a pull request before merging: ✓
Require approvals: 1
Dismiss stale PR approvals when new commits are pushed: ✓
Require status checks to pass before merging: ✓
Require branches to be up to date before merging: ✓
```

---

## 2. Project Template Setup

### Access Internal Template Repository

Our internal template repository contains pre-configured templates for common project types.


**Note:** These templates are currently internal to the Futured organization and not publicly available. We plan to open-source these templates in the future to share our best practices with the broader developer community.

### Available Templates

1. REST or GraphQL API inside NestJs project
2. PostgreSQL or MongoDB database connection
3. Vue.js or Nuxt frontend for administration
4. Github Actions
5. Terraform scripts

### Template Usage Process

#### Update Package.json
```json
{
  "name": "your-project-name",
  "version": "1.0.0",
  "description": "Your project description",
     "repository": {
     "type": "git",
     "url": "git+[https://github.com/futuredapp/your-project-name.git](https://github.com/futuredapp/your-project-name.git)"
   },
  "author": "Futured",
  "license": "MIT"
}
```

#### Update Docker Configuration
```yaml
# docker-compose.dist.yml
version: '3.7'

services:
  postgres:
    environment:
      POSTGRES_DB: your-project-db
      POSTGRES_USER: your-project-user
      POSTGRES_PASSWORD: your-project-password

  api:
    environment:
      DATABASE_URL: postgresql://your-project-user:your-project-password@postgres/your-project-db
      # Update other environment variables as needed
```

#### Update Environment Variables
```bash
# .env.example
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/database
# Add project-specific variables
```

### Customize Template Code

1. **Remove Template-Specific Code**
   ```bash
   # Remove example endpoints, models, and services
   rm -rf src/examples
   rm -rf src/template-*
   ```

2. **Update Application Configuration**
   ```typescript
   // src/app.module.ts
   @Module({
     imports: [
       // Keep only what you need
       DatabaseModule,
       AuthModule,
       // Add your modules
     ],
     controllers: [],
     providers: [],
   })
   export class AppModule {}
   ```

3. **Update Database Schema**
   ```sql
   -- Remove template tables and create your own
   DROP TABLE IF EXISTS template_examples;
   -- Add your tables
   ```

---

## 3. README Setup

### Create Comprehensive README.md

```markdown
# Project Name

Brief description of what this project does and why it exists.

## 🚀 Quick Start

### Prerequisites
- Node.js (version specified in .nvmrc)
- Docker Desktop
- Git

### Installation
```bash
 # Clone the repository
 git clone [git@github.com:futuredapp/your-project-name.git](https://github.com/futuredapp/your-project-name.git)
 cd your-project-name

# Install dependencies
npm install

# Copy environment template
cp docker-compose.dist.yml docker-compose.yml

# Edit environment variables
nano docker-compose.yml

# Start the application
docker-compose up -d
```

## 4. Set Up CI/CD Pipeline

1. Copy Template Workflows
2. Customize Workflows for Your Project
3. Environment secrets are configured in the cloud deployment platform, no local secrets management required