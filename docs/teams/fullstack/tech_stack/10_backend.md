# Backend

Our backend stack is built on NestJS with Node.js, supporting both REST and GraphQL APIs. We emphasize modular architecture, type safety, and testability.

## Architecture Overview

![](./Resources/10_backend_arch_overview_light.png#only-light){data-gallery="light"}
![](./Resources/10_backend_arch_overview_dark.png#only-dark){data-gallery="dark"}

## Core Technologies

### NestJS

NestJS is our primary backend framework. It provides a structured, opinionated architecture with first-class TypeScript support, dependency injection, and a modular design that scales from small APIs to complex applications.

**Why NestJS:**

- **Modular architecture** — Encourages separation of concerns and code reuse across projects
- **Dependency injection** — Built-in IoC container for clean, testable service composition
- **Protocol agnostic** — Same codebase can serve REST, GraphQL, WebSockets, and gRPC
- **Rich ecosystem** — First-party packages for auth, config, caching, queues, scheduling, and more
- **TypeScript native** — Designed from the ground up for TypeScript, not retrofitted
- **Enterprise-grade** — Battle-tested patterns for validation, error handling, logging, and security

**Trade-offs to consider:**

- NestJS adds abstraction overhead — for simple APIs, this can mean higher development cost for clients
- The learning curve is steeper compared to plain Express/Fastify
- Some client budgets or project scopes don't justify the full NestJS setup

### Choosing a Backend Framework

NestJS is the **default**, not a mandate. For projects where NestJS is overkill, consider lighter alternatives:

| Framework | When to use |
|---|---|
| **NestJS** | Medium-to-large projects, complex business logic, multiple modules, long-term maintenance |
| **Express / Fastify** | Small APIs, microservices, tight budgets, simple CRUD |
| **Hono** | Edge-first APIs, serverless functions, minimal footprint |

The decision should be made at project kickoff based on scope, budget, and expected complexity. Document the choice in the project README.

### Request Lifecycle

Every incoming request passes through a well-defined pipeline of NestJS components:

![](./Resources/10_backend_request_lifecycle_light.png#only-light){data-gallery="light"}
![](./Resources/10_backend_request_lifecycle_dark.png#only-dark){data-gallery="dark"}


Each layer has a clear responsibility:

| Layer | Responsibility | Example |
|---|---|---|
| **Middleware** | Request preprocessing | CORS, body parsing, request logging |
| **Guards** | Authentication & authorization | JWT validation, role checks |
| **Interceptors** | Cross-cutting concerns | Response mapping, caching, timing |
| **Pipes** | Data validation & transformation | DTO validation, type coercion |
| **Controllers/Resolvers** | Route handling | Map HTTP/GraphQL requests to services |
| **Services** | Business logic | Core application functionality |
| **Filters** | Error handling | Standardized error responses |

### Module Structure

Every NestJS project follows a modular architecture:

```
src/
├── app.module.ts           # Root module
├── shared/                 # Shared utilities, guards, interceptors
│   ├── guards/
│   ├── interceptors/
│   ├── filters/
│   └── decorators/
├── config/                 # Configuration and validation
├── auth/                   # Authentication module
│   ├── auth.module.ts
│   ├── auth.service.ts
│   ├── auth.controller.ts
│   └── strategies/
├── users/                  # Feature module
│   ├── users.module.ts
│   ├── users.service.ts
│   ├── users.controller.ts (REST) or users.resolver.ts (GraphQL)
│   ├── dto/
│   └── entities/
└── ...
```

**Guidelines:**

- One module per domain entity or feature area
- Modules expose only what's needed via `exports`
- Shared logic goes in `shared/`
- Configuration is validated at startup using `@nestjs/config` with Zod or class-validator

## API Patterns

### REST

For standard CRUD operations and simple APIs, we use REST controllers:

```typescript
@Controller('users')
export class UsersController {
    constructor(private readonly usersService: UsersService) {}

    @Get()
    findAll(@Query() query: PaginationDto): Promise<PaginatedResponse<User>> {
        return this.usersService.findAll(query)
    }

    @Get(':id')
    findOne(@Param('id', ParseUUIDPipe) id: string): Promise<User> {
        return this.usersService.findOne(id)
    }

    @Post()
    create(@Body() dto: CreateUserDto): Promise<User> {
        return this.usersService.create(dto)
    }
}
```

### GraphQL

For complex data requirements or when the frontend needs flexible queries, we use GraphQL with the code-first approach:

```typescript
@Resolver(() => User)
export class UsersResolver {
    constructor(private readonly usersService: UsersService) {}

    @Query(() => [User])
    async users(): Promise<User[]> {
        return this.usersService.findAll()
    }

    @Mutation(() => User)
    async createUser(@Args('input') input: CreateUserInput): Promise<User> {
        return this.usersService.create(input)
    }

    @ResolveField(() => [Project])
    async projects(@Parent() user: User): Promise<Project[]> {
        return this.usersService.getProjects(user.id)
    }
}
```

### Choosing an API Style

**REST is the default.** GraphQL adds complexity (schema management, N+1 queries, caching challenges) and should only be used when its benefits clearly outweigh the cost.

![](./Resources/10_backend_rest_vs_graphql_light.png#only-light){data-gallery="light"}
![](./Resources/10_backend_rest_vs_graphql_dark.png#only-dark){data-gallery="dark"}


| Use REST when... | Use GraphQL when... |
|---|---|
| Simple CRUD operations | Complex nested data relationships |
| Public APIs for third parties | Frontend needs flexible queries |
| File uploads / downloads | Multiple related entities per request |
| Webhooks and callbacks | Real-time subscriptions needed |
| **Most projects (default)** | **Only when justified by data complexity** |

!!! info "GraphQL Considerations"

    GraphQL is powerful but adds operational cost: schema versioning, query complexity limits, caching strategies, and tooling overhead. Don't adopt it just because it's modern — adopt it when the frontend genuinely benefits from flexible queries over multiple related entities.

## Validation

All incoming data is validated using DTOs with `class-validator`:

```typescript
// dto/create-user.dto.ts
export class CreateUserDto {
    @IsEmail()
    email: string

    @IsString()
    @MinLength(2)
    @MaxLength(100)
    name: string

    @IsEnum(UserRole)
    role: UserRole
}
```

## Architecture: Monolith vs. Services

**Start with a modular monolith.** Only split into dedicated services when there is a clear, justified need.

| Approach | When to use |
|---|---|
| **Modular monolith** | Most projects. Single deployable, modules separated by NestJS module boundaries. Simpler ops, easier debugging. |
| **Dedicated services** | When parts of the system have fundamentally different scaling, deployment, or team ownership needs. |

**Signs you need to split:**

- One part of the system needs independent scaling (e.g. heavy background processing)
- Different release cadences for different parts
- Separate teams owning separate domains
- A module's failure should not bring down the rest

**Rules for services:**

- Each service owns its data — no shared databases
- Services communicate via REST APIs or message queues, not direct DB access
- Every service must be independently deployable and have its own CI/CD pipeline
- Document service boundaries and communication contracts

## Node.js Runtime

- We use the LTS version of Node.js specified in each project's `.nvmrc`
- Package manager: **yarn** (primary) — some projects may use npm
- All projects include `engines` field in `package.json` to enforce Node.js version
