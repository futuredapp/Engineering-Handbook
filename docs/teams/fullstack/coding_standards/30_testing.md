# Testing Standards

This guide covers our testing philosophy, tooling, and conventions for full-stack projects.

## Testing Philosophy

- **Test behavior, not implementation** — tests should verify what the code does, not how it does it
- **Prefer integration tests** over unit tests for backend services — they catch more real bugs with less maintenance
- **Every bug fix includes a test** — reproduce the bug with a failing test before fixing it
- **Tests are documentation** — well-named tests explain the expected behavior of the system

## Testing Pyramid

```
        ┌─────────┐
        │   E2E   │  Few, slow, high confidence
        ├─────────┤
        │ Integr. │  Moderate count, test real interactions
        ├─────────┤
        │  Unit   │  Many, fast, focused on logic
        └─────────┘
```

| Layer | What it tests | Tools | Speed |
|---|---|---|---|
| Unit | Pure functions, utilities, isolated logic | Jest | Fast |
| Integration | Service methods with database, API endpoints | Jest + test DB | Medium |
| E2E | Full user flows through the browser or API | Playwright / Cypress | Slow |

## Backend Testing

### Unit Tests

Use for pure business logic, utility functions, and DTOs:

```typescript
// users.service.spec.ts
describe('UsersService', () => {
    describe('validateEmail', () => {
        it('should accept valid email addresses', () => {
            expect(validateEmail('user@example.com')).toBe(true)
        })

        it('should reject invalid email addresses', () => {
            expect(validateEmail('not-an-email')).toBe(false)
        })
    })
})
```

### Integration Tests

Test NestJS modules with real database connections:

```typescript
// users.service.integration.spec.ts
describe('UsersService (integration)', () => {
    let service: UsersService
    let module: TestingModule

    beforeAll(async () => {
        module = await Test.createTestingModule({
            imports: [AppModule],
        }).compile()

        service = module.get(UsersService)
    })

    afterAll(async () => {
        await module.close()
    })

    it('should create and retrieve a user', async () => {
        const user = await service.create({
            email: 'test@example.com',
            name: 'Test User',
        })

        const found = await service.findOne(user.id)
        expect(found.email).toBe('test@example.com')
    })
})
```

### API Tests

Test HTTP endpoints end-to-end:

```typescript
// users.e2e.spec.ts
describe('Users API', () => {
    let app: INestApplication

    beforeAll(async () => {
        const module = await Test.createTestingModule({
            imports: [AppModule],
        }).compile()

        app = module.createNestApplication()
        await app.init()
    })

    it('POST /users should create a user', async () => {
        const response = await request(app.getHttpServer())
            .post('/users')
            .send({ email: 'new@example.com', name: 'New User' })
            .expect(201)

        expect(response.body).toHaveProperty('id')
        expect(response.body.email).toBe('new@example.com')
    })
})
```

## Frontend Testing

### E2E Tests

Use Playwright for end-to-end browser testing:

```typescript
// tests/e2e/login.spec.ts
import { test, expect } from '@playwright/test'

test('user can log in', async ({ page }) => {
    await page.goto('/login')
    await page.fill('[data-testid="email"]', 'user@example.com')
    await page.fill('[data-testid="password"]', 'password123')
    await page.click('[data-testid="submit"]')

    await expect(page).toHaveURL('/dashboard')
    await expect(page.locator('[data-testid="welcome"]')).toContainText('Welcome')
})
```

## Conventions

### Test File Naming

- Unit/integration tests: `<name>.spec.ts` (co-located with source)
- E2E tests: `tests/e2e/<feature>.spec.ts`

### Test Naming

Use descriptive `describe` and `it` blocks that read as sentences:

```typescript
describe('UsersService', () => {
    describe('create', () => {
        it('should create a user with valid data', () => { ... })
        it('should throw ConflictException for duplicate email', () => { ... })
        it('should hash the password before storing', () => { ... })
    })
})
```

### Data Test IDs

Use `data-testid` attributes for E2E selectors instead of CSS classes or element types:

```vue
<button data-testid="submit-btn">Submit</button>
```

## Coverage

- We do not enforce strict coverage thresholds — coverage is a guide, not a goal
- Focus on testing critical paths: authentication, payments, data mutations
- New features should include tests for the happy path and key edge cases
- CI runs all tests on every PR — failing tests block the merge

## Tools Summary

| Tool | Purpose |
|---|---|
| **Jest** | Unit and integration testing (default) |
| **Playwright** | E2E browser testing (preferred) |
| **Cypress** | E2E browser testing (legacy projects) |
| **supertest** | HTTP endpoint testing in NestJS |
