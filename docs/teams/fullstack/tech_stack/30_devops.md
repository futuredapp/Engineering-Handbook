# DevOps & Infrastructure

Our infrastructure is containerized, automated, and deployed via CI/CD pipelines. We run on Google Cloud Platform (GCP) and Digital Ocean, managed with Terraform.

## Infrastructure Overview

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
graph TB
    subgraph Dev["Developer"]
        Code([Code + Push]):::dev
    end

    subgraph GH["GitHub"]
        Repo{{Repository}}:::github
        Actions{{GitHub Actions<br/>CI/CD}}:::github
        Registry[(Container Registry)]:::github
    end

    subgraph Cloud["Cloud Platform — GCP / Digital Ocean"]
        LB[[Load Balancer<br/>SSL Termination]]:::infra

        subgraph Compute["Compute"]
            API_Dev([API — Dev]):::compute_dev
            API_Prod([API — Prod]):::compute_prod
        end

        subgraph Data["Data"]
            DB_Dev[(DB — Dev)]:::data_dev
            DB_Prod[(DB — Prod)]:::data_prod
            Cache_Dev[(Redis — Dev)]:::data_dev
            Cache_Prod[(Redis — Prod)]:::data_prod
        end

        subgraph Storage["Storage"]
            Secrets[/Secret Manager/]:::storage
            Files[/Cloud Storage / Spaces/]:::storage
        end
    end

    subgraph Mon["Monitoring"]
        Sentry([Sentry]):::monitoring
        Logs([Cloud Logging]):::monitoring
    end

    Code --> Repo
    Repo --> Actions
    Actions -->|Build & Push| Registry
    Actions -->|Deploy| API_Dev
    Actions -->|Deploy| API_Prod
    LB --> API_Dev
    LB --> API_Prod
    API_Dev --> DB_Dev
    API_Dev --> Cache_Dev
    API_Prod --> DB_Prod
    API_Prod --> Cache_Prod
    API_Dev --> Secrets
    API_Prod --> Secrets
    API_Dev --> Files
    API_Prod --> Files
    API_Dev --> Sentry
    API_Prod --> Sentry
    API_Dev --> Logs
    API_Prod --> Logs

    classDef dev fill:#64748b,stroke:#334155,color:#fff
    classDef github fill:#24292e,stroke:#1b1f23,color:#fff
    classDef infra fill:#f59e0b,stroke:#b45309,color:#fff
    classDef compute_dev fill:#22c55e,stroke:#15803d,color:#fff
    classDef compute_prod fill:#e11d48,stroke:#9f1239,color:#fff
    classDef data_dev fill:#059669,stroke:#065f46,color:#fff
    classDef data_prod fill:#dc2626,stroke:#991b1b,color:#fff
    classDef storage fill:#8b5cf6,stroke:#5b21b6,color:#fff
    classDef monitoring fill:#06b6d4,stroke:#0e7490,color:#fff
```

## Docker

Every project is containerized for both development and production.

### Development

We use Docker Compose for local development environments:

```yaml
# docker-compose.dist.yml
services:
  api:
    build:
      context: ./api
      dockerfile: dev.Dockerfile
    volumes:
      - ./api/src:/app/src
    ports:
      - "8080:8080"
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

### Production

Production containers use multi-stage builds for minimal image size:

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

EXPOSE 8080
CMD ["node", "dist/main.js"]
```

## CI/CD Pipeline

All projects use GitHub Actions for continuous integration and deployment. The specific workflow varies per project, but the process follows the same stages:

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
graph LR
    subgraph CI["CI — Every PR"]
        Lint([Lint<br/>ESLint + Prettier]):::lint --> Test([Test<br/>Unit + Integration]):::test
        Test --> Build([Build<br/>TypeScript + Docker]):::build
    end

    subgraph CD["CD — On Merge"]
        Push{{Push Image<br/>to Registry}}:::push --> Deploy_Dev([Deploy to Dev]):::dev
        Push --> Deploy_Prod([Deploy to Prod]):::prod
    end

    Build --> Push

    classDef lint fill:#f59e0b,stroke:#b45309,color:#fff
    classDef test fill:#3b82f6,stroke:#1e40af,color:#fff
    classDef build fill:#8b5cf6,stroke:#5b21b6,color:#fff
    classDef push fill:#06b6d4,stroke:#0e7490,color:#fff
    classDef dev fill:#22c55e,stroke:#15803d,color:#fff
    classDef prod fill:#e11d48,stroke:#9f1239,color:#fff
```

1. **Lint** — ESLint + Prettier checks ensure code quality
2. **Test** — Unit and integration tests verify functionality
3. **Build** — TypeScript compilation and Docker image build verify everything compiles
4. **Push** — Docker image is tagged with the commit SHA and pushed to the container registry
5. **Deploy** — The new image is deployed to the target environment

See [Deployment Process](../deployment/00_deployment.md) for detailed deployment procedures.

## Hosting Platforms

### Google Cloud Platform (GCP)

Used for larger or more complex projects:

- **Cloud Run** — Serverless container hosting (primary compute)
- **Cloud SQL** — Managed PostgreSQL
- **Cloud Storage** — File storage and static assets
- **Secret Manager** — Secrets management
- **Cloud Build** — Container image builds (alternative to GitHub Actions)

### Digital Ocean

Used for smaller projects or when simpler infrastructure is preferred:

- **App Platform** — Container and static site hosting
- **Managed Database** — PostgreSQL and Redis
- **Spaces** — Object storage (S3-compatible)

### Choosing a Platform

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
graph TD
    Start{New Project}:::decision --> Q1{Enterprise client?<br/>Complex scaling needs?}:::decision
    Q1 -->|Yes| GCP([Google Cloud Platform]):::gcp
    Q1 -->|No| Q2{Tight budget?<br/>Simple infrastructure?}:::decision
    Q2 -->|Yes| DO([Digital Ocean]):::do
    Q2 -->|No| Q3{Client preference?}:::decision
    Q3 -->|GCP| GCP
    Q3 -->|DO| DO
    Q3 -->|No preference| DO_Default([Digital Ocean<br/>Simpler default]):::do

    classDef decision fill:#f59e0b,stroke:#b45309,color:#fff
    classDef gcp fill:#4285f4,stroke:#1a73e8,color:#fff
    classDef do fill:#0080ff,stroke:#0060cc,color:#fff
```

| | GCP | Digital Ocean |
|---|---|---|
| Best for | Complex apps, scaling needs, enterprise clients | Simpler apps, tighter budgets, quick setup |
| Pricing model | Pay-per-use | Predictable monthly pricing |
| Managed services | Extensive (50+ services) | Focused (core services) |
| Terraform support | Full | Full |

The choice is made per project based on client requirements, budget, and complexity.

## Infrastructure as Code

Some projects use Infrastructure as Code (IaC) to define and manage cloud resources. This is not a default across all projects — it's adopted where the infrastructure complexity justifies it.

- **Terraform** — Currently used on a few projects. Declarative HCL syntax, mature ecosystem, strong GCP and Digital Ocean support.
- **Pulumi** — We are experimenting with Pulumi as an alternative. It allows defining infrastructure in TypeScript, which fits naturally into our stack.

When IaC is used, the general principles apply:

- Infrastructure state is stored remotely (e.g. GCS bucket, Terraform Cloud, Pulumi Cloud)
- Changes go through PR review, just like application code
- Environment-specific configuration is separated from the resource definitions

## Environment Promotion

Code flows through environments in this order:

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
graph LR
    Dev([develop branch<br/>Dev Environment]):::dev --> Staging([staging branch<br/>Staging Environment]):::staging
    Staging --> Prod([main branch<br/>Production]):::prod

    classDef dev fill:#22c55e,stroke:#15803d,color:#fff
    classDef staging fill:#f59e0b,stroke:#b45309,color:#fff
    classDef prod fill:#e11d48,stroke:#9f1239,color:#fff
```

Each environment has its own:

- Database instance
- Environment variables and secrets
- Domain / URL
- Resource allocation (dev uses smaller instances)
