# API Documentation

All APIs must be documented in a way that allows consumers (frontend developers, external clients, other teams) to understand and use them without reading the source code.

## REST APIs — OpenAPI / Swagger

NestJS has built-in support for generating OpenAPI (Swagger) documentation from decorators.

### Setup

```typescript
// main.ts
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger'

const config = new DocumentBuilder()
    .setTitle('MyApp API')
    .setDescription('API documentation for MyApp')
    .setVersion('1.0')
    .addBearerAuth()
    .build()

const document = SwaggerModule.createDocument(app, config)
SwaggerModule.setup('api/docs', app, document)
```

The Swagger UI is available at `/api/docs` in development and staging environments.

!!! warning "Production Access"
    Disable or restrict access to Swagger UI in production. Use a guard or environment check to prevent public access to API documentation.

### Decorating Endpoints

```typescript
@ApiTags('Users')
@Controller('users')
export class UsersController {

    @ApiOperation({ summary: 'Create a new user' })
    @ApiResponse({ status: 201, description: 'User created successfully', type: UserResponseDto })
    @ApiResponse({ status: 409, description: 'User with this email already exists' })
    @Post()
    create(@Body() dto: CreateUserDto): Promise<UserResponseDto> {
        return this.usersService.create(dto)
    }

    @ApiOperation({ summary: 'Get a user by ID' })
    @ApiParam({ name: 'id', description: 'User UUID' })
    @ApiResponse({ status: 200, type: UserResponseDto })
    @ApiResponse({ status: 404, description: 'User not found' })
    @Get(':id')
    findOne(@Param('id', ParseUUIDPipe) id: string): Promise<UserResponseDto> {
        return this.usersService.findOne(id)
    }
}
```

### DTO Documentation

Use `@ApiProperty` decorators on DTOs so Swagger can generate accurate schemas:

```typescript
export class CreateUserDto {
    @ApiProperty({ example: 'john@example.com', description: 'User email address' })
    @IsEmail()
    email: string

    @ApiProperty({ example: 'John Doe', minLength: 2, maxLength: 100 })
    @IsString()
    @MinLength(2)
    name: string

    @ApiProperty({ enum: UserRole, example: UserRole.EDITOR })
    @IsEnum(UserRole)
    role: UserRole
}
```

### Best Practices

- Document all public endpoints with `@ApiOperation` and `@ApiResponse`
- Include example values in `@ApiProperty` — they populate the "Try it out" feature
- Group related endpoints with `@ApiTags`
- Use separate response DTOs (don't expose entity internals like passwords or internal IDs)
- Keep Swagger descriptions concise — they supplement the endpoint names, not replace them

## GraphQL APIs

GraphQL is self-documenting by design — the schema serves as the API contract.

### Schema Documentation

Add descriptions to types and fields using the `description` option:

```typescript
@ObjectType({ description: 'A registered user of the application' })
export class User {
    @Field(() => ID)
    id: string

    @Field({ description: 'The user display name' })
    name: string

    @Field(() => UserRole, { description: 'The user permission level' })
    role: UserRole
}
```

### GraphQL Playground

GraphQL Playground (or Apollo Sandbox) is available at the GraphQL endpoint in development. It provides:

- Interactive query builder
- Auto-complete based on the schema
- Documentation explorer showing all types, queries, and mutations

```typescript
// app.module.ts
GraphQLModule.forRoot<ApolloDriverConfig>({
    driver: ApolloDriver,
    autoSchemaFile: true,
    playground: process.env.NODE_ENV !== 'production',
})
```

### Schema Conventions

- Name queries and mutations entity-first: `userCreate`, `projectSettingsUpdate` (groups operations by entity in schema explorers and IDE autocomplete instead of scattering them under `create*` / `update*`)
- Input types end with `Input`, matching the operation name: `UserCreateInput`, `ProjectSettingsUpdateInput`
- Return the mutated object from mutations (not just a success boolean)
- Document nullable fields and their meaning

## API Versioning

For REST APIs with external consumers, version the API when making breaking changes:

```typescript
// Prefix-based versioning (recommended)
@Controller({ path: 'users', version: '1' })
export class UsersV1Controller { ... }

@Controller({ path: 'users', version: '2' })
export class UsersV2Controller { ... }
```

```typescript
// main.ts
app.enableVersioning({
    type: VersioningType.URI, // Results in /v1/users, /v2/users
})
```

**When to version:**

- Removing or renaming fields in the response
- Changing the meaning or format of existing fields
- Removing endpoints

**When versioning is NOT needed:**

- Adding new optional fields to responses
- Adding new endpoints
- Internal APIs consumed only by our own frontend
