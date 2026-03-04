# Code Documentation Standards

Good documentation helps new team members onboard faster and makes codebases easier to maintain. This guide covers when and how to document code.

## Philosophy

- **Code should be self-documenting** — use clear naming, small functions, and obvious structure
- **Document the why, not the what** — if you need to explain *what* the code does, consider rewriting it to be clearer
- **Don't over-document** — unnecessary comments become noise and go stale

## When to Document

**Always document:**

- REST API endpoints — via Swagger/OpenAPI decorators (see [API Documentation](10_api_docs.md))
- GraphQL types and fields — via `description` annotations
- Complex business logic that isn't obvious from the code
- Non-obvious decisions ("we do X instead of Y because...")
- Workarounds and known limitations
- Configuration options

**Skip documentation for:**

- Self-evident code (`getUserById` doesn't need a comment)
- Internal utility functions with clear naming
- Code that changes frequently (comments go stale)

## Public API Documentation

For REST endpoints, we use **Swagger/OpenAPI decorators** — not JSDoc. See [API Documentation](10_api_docs.md) for details on `@ApiOperation`, `@ApiResponse`, and `@ApiProperty`.

For GraphQL, we use the built-in **`description` option** on `@ObjectType`, `@Field`, `@Query`, and `@Mutation` decorators. The schema itself serves as documentation.

## Service Method Comments

For internal service methods with non-obvious behavior, use JSDoc comments:

```typescript
/**
 * Creates a new user account and sends a welcome email.
 *
 * @throws ConflictException if a user with this email already exists
 * @throws BadRequestException if the email domain is blacklisted
 */
async createUser(dto: CreateUserDto): Promise<User> {
    // ...
}
```

**Guidelines:**

- Use `@throws` to document exceptions a caller should handle
- Use `@returns` when the return value isn't obvious from the type signature
- Use `@param` only when parameter names alone aren't sufficient
- Skip JSDoc for simple CRUD methods — the types and Swagger decorators say enough

## Inline Comments

Use inline comments sparingly, for non-obvious logic:

```typescript
// PostgreSQL error code 23505 = unique constraint violation
if (error.code === '23505') {
    throw new ConflictException('Email already exists')
}
```

```typescript
// We fetch N+1 items to determine if there's a next page
// without running a separate COUNT query
const items = await repo.find({ take: limit + 1 })
```

**Avoid:**

```typescript
// BAD: states the obvious
// Get user by ID
const user = await this.usersService.findById(id)

// BAD: restates the type
// The user's email address (string)
email: string
```

## Project README

Every project repository must have a `README.md` with:

1. **Project description** — What it does, who it's for
2. **Quick start** — Steps to get the project running locally
3. **Environment variables** — Table of required variables with descriptions
4. **Available scripts** — What `npm run dev`, `npm run test`, etc. do
5. **Architecture overview** — Brief description of the project structure (for larger projects)
6. **Deployment** — How and where the project is deployed

### Template

```markdown
# Project Name

Brief description of the project.

## Quick Start

1. Clone the repository
2. `cp docker-compose.dist.yml docker-compose.yml`
3. Fill in environment variables
4. `docker compose up -d`
5. `docker compose exec api npm run migrate`

## Environment Variables

| Variable | Description | Required |
|---|---|---|
| `DATABASE_URL` | PostgreSQL connection string | Yes |
| `JWT_SECRET` | Secret for signing JWTs | Yes |

## Scripts

| Script | Description |
|---|---|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm run test` | Run tests |
| `npm run lint` | Lint and format check |

## Architecture

Brief overview of the project structure and key modules.

## Deployment

Deployed via GitHub Actions to [platform]. See `.github/workflows/` for details.
```

## Changelog

For projects with external consumers (APIs used by other teams or clients), maintain a `CHANGELOG.md` following the [Keep a Changelog](https://keepachangelog.com/) format. For internal projects, git history and PR descriptions are sufficient.
