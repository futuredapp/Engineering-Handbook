# Database

We use PostgreSQL as our primary relational database and MongoDB for specific use cases. Database access is primarily through an ORM or query builder. Raw SQL queries are acceptable when the query is too complex to express cleanly with the ORM.

## Data Architecture

![](./Resources/20_database_data_architecture_light.png#only-light){data-gallery="light"}
![](./Resources/20_database_data_architecture_dark.png#only-dark){data-gallery="dark"}


## PostgreSQL

PostgreSQL is the default choice for all new projects. It provides strong ACID compliance, excellent JSON support, and a rich feature set that covers the vast majority of our data requirements.

**Why PostgreSQL:**

- **Reliability** — Proven in production at scale across thousands of organizations worldwide
- **ACID compliance** — Full transactional support with referential integrity
- **Rich data types** — Native JSON/JSONB, arrays, enums, UUID, and more
- **Performance** — Advanced query planner, indexing strategies, and parallel query execution
- **Extensibility** — Full-text search, PostGIS for geospatial data, and a rich extension ecosystem
- **Managed hosting** — Available as a managed service on both GCP (Cloud SQL) and Digital Ocean

**When we use PostgreSQL:**

- Structured, relational data with well-defined schemas
- Applications requiring transactions and referential integrity
- Most CRUD-based projects (which covers the majority of our work)

## MongoDB

MongoDB is used in projects where the data model is inherently document-oriented or when working with highly variable schemas.

**When we use MongoDB:**

- Document-oriented data (CMS content, logs, event streams)
- Rapidly evolving schemas during prototyping
- When explicitly required by a project's data model

## ORM Choices

### TypeORM

TypeORM is our most commonly used ORM, with deep NestJS integration via `@nestjs/typeorm`.

```typescript
// entities/user.entity.ts
@Entity()
export class User {
    @PrimaryGeneratedColumn('uuid')
    id: string

    @Column({ unique: true })
    email: string

    @Column()
    name: string

    @CreateDateColumn()
    createdAt: Date

    @OneToMany(() => Project, (project) => project.owner)
    projects: Project[]
}
```

### Prisma & Drizzle (Experimental)

We are experimenting with **Prisma** and **Drizzle** in prototypes and internal projects. Neither is currently used in production.

- **Prisma** — Type-safe query API, schema-first approach, excellent migration tooling
- **Drizzle** — Lightweight, SQL-like control with full type safety and minimal overhead

These may become recommended alternatives in the future as we gain more experience with them in real projects.

## Migration Strategy

Database schema changes are always managed through migrations — never manual DDL.

![](./Resources/20_database_migration_strategy_light.png#only-light){data-gallery="light"}
![](./Resources/20_database_migration_strategy_dark.png#only-dark){data-gallery="dark"}


### Rules

1. **Every schema change gets a migration** — no exceptions
2. **Migrations are committed to version control** alongside the code that uses them
3. **Migrations run automatically** in CI/CD before the application starts
4. **Never edit an existing migration** that has been applied to any environment
5. **Test migrations** against a copy of production data when possible

### Example: Prisma Migrations

```bash
# Create a migration after changing schema.prisma
yarn prisma migrate dev --name add_user_role

# Apply migrations in production
yarn prisma migrate deploy
```

### Example: TypeORM Migrations

```bash
# Generate a migration from entity changes
yarn typeorm migration:generate -n AddUserRole

# Run pending migrations
yarn typeorm migration:run
```

## Connection Management

- Use connection pooling (configured in the ORM or via PgBouncer for high-traffic apps)
- Set reasonable pool sizes: `min: 2, max: 10` for development, adjust for production based on load
- Always close connections gracefully on app shutdown

## Backups

- **Production**: Automated daily backups via the hosting provider (GCP Cloud SQL / Digital Ocean Managed Database)
- **Staging**: Weekly backups or on-demand snapshots
- **Development**: No backups needed — use seed scripts to recreate data
