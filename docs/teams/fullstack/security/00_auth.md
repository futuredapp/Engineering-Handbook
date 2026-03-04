# Authentication & Authorization

This guide covers how we implement authentication (who are you?) and authorization (what can you do?) in our NestJS applications.

## JWT-Based Authentication

JWT (JSON Web Token) is our primary authentication mechanism for API-based applications.

### Token Flow

```
1. User sends credentials (POST /auth/login)
2. Server validates credentials against the database
3. Server returns an access token (+ optional refresh token)
4. Client sends the access token in the Authorization header for subsequent requests
5. Server validates the token on each request via a guard
```

### NestJS Implementation

#### Auth Module

```typescript
// auth/auth.module.ts
@Module({
    imports: [
        UsersModule,
        JwtModule.registerAsync({
            inject: [ConfigService],
            useFactory: (config: ConfigService) => ({
                secret: config.getOrThrow('JWT_SECRET'),
                signOptions: { expiresIn: '15m' },
            }),
        }),
    ],
    controllers: [AuthController],
    providers: [AuthService, JwtStrategy],
})
export class AuthModule {}
```

#### Auth Service

```typescript
// auth/auth.service.ts
@Injectable()
export class AuthService {
    constructor(
        private readonly usersService: UsersService,
        private readonly jwtService: JwtService,
    ) {}

    async login(dto: LoginDto): Promise<TokenResponse> {
        const user = await this.usersService.findByEmail(dto.email)
        if (!user || !(await bcrypt.compare(dto.password, user.passwordHash))) {
            throw new UnauthorizedException('Invalid credentials')
        }

        const payload: JwtPayload = { sub: user.id, email: user.email, role: user.role }

        return {
            accessToken: this.jwtService.sign(payload),
            refreshToken: this.jwtService.sign(payload, { expiresIn: '7d' }),
        }
    }
}
```

#### JWT Strategy (Passport)

```typescript
// auth/strategies/jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
    constructor(configService: ConfigService) {
        super({
            jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
            secretOrKey: configService.getOrThrow('JWT_SECRET'),
        })
    }

    validate(payload: JwtPayload): JwtPayload {
        return payload
    }
}
```

### Token Storage (Frontend)

| Storage | Pros | Cons | Use when |
|---|---|---|---|
| HTTP-only cookie | Safe from XSS | Requires CSRF protection | SSR apps, same-domain API |
| Memory (variable) | Safe from XSS and CSRF | Lost on page refresh | SPAs with refresh token flow |
| localStorage | Persists across tabs | Vulnerable to XSS | Avoid for sensitive tokens |

**Our recommendation:** Use HTTP-only, Secure, SameSite cookies for access tokens when the API and frontend share the same domain. For cross-domain setups, use in-memory storage with a refresh token in an HTTP-only cookie.

## OAuth2 / Social Login

For applications that support social login:

```typescript
// auth/strategies/google.strategy.ts
@Injectable()
export class GoogleStrategy extends PassportStrategy(Strategy, 'google') {
    constructor(configService: ConfigService) {
        super({
            clientID: configService.getOrThrow('GOOGLE_CLIENT_ID'),
            clientSecret: configService.getOrThrow('GOOGLE_CLIENT_SECRET'),
            callbackURL: configService.getOrThrow('GOOGLE_CALLBACK_URL'),
            scope: ['email', 'profile'],
        })
    }

    async validate(accessToken: string, refreshToken: string, profile: Profile): Promise<User> {
        // Find or create user from Google profile
    }
}
```

**Common providers:**

- Google OAuth (most common)
- Apple Sign-In (required when offering social login on iOS)
- Other providers can be integrated as needed using Passport strategies (Facebook, GitHub, enterprise SSO, etc.)

## Firebase Authentication

For projects where we don't need custom authentication logic, we use **Firebase Authentication** as a managed solution. Firebase handles user registration, login, password resets, and social login out of the box.

**When to use Firebase Auth:**

- Mobile apps or projects already using Firebase services
- When rapid setup is more important than full customization
- Projects that need multiple auth providers with minimal backend code

**Integration with NestJS:**

```typescript
// auth/firebase-auth.guard.ts
@Injectable()
export class FirebaseAuthGuard implements CanActivate {
    constructor(private readonly firebaseService: FirebaseService) {}

    async canActivate(context: ExecutionContext): Promise<boolean> {
        const request = context.switchToHttp().getRequest()
        const token = request.headers.authorization?.replace('Bearer ', '')

        if (!token) throw new UnauthorizedException()

        const decodedToken = await this.firebaseService.verifyIdToken(token)
        request.user = decodedToken
        return true
    }
}
```

Firebase Auth can be combined with our own authorization layer (guards, roles) — Firebase handles identity, our backend handles permissions.

## Authorization — Guards & Roles

### Role-Based Access Control (RBAC)

```typescript
// common/decorators/roles.decorator.ts
export const Roles = (...roles: UserRole[]) => SetMetadata('roles', roles)

// common/guards/roles.guard.ts
@Injectable()
export class RolesGuard implements CanActivate {
    constructor(private reflector: Reflector) {}

    canActivate(context: ExecutionContext): boolean {
        const requiredRoles = this.reflector.getAllAndOverride<UserRole[]>('roles', [
            context.getHandler(),
            context.getClass(),
        ])
        if (!requiredRoles) return true

        const { user } = context.switchToHttp().getRequest()
        return requiredRoles.includes(user.role)
    }
}
```

### Using Guards on Endpoints

```typescript
@Controller('admin/users')
@UseGuards(JwtAuthGuard, RolesGuard)
export class AdminUsersController {

    @Get()
    @Roles(UserRole.ADMIN)
    findAll(): Promise<User[]> {
        return this.usersService.findAll()
    }

    @Delete(':id')
    @Roles(UserRole.ADMIN)
    remove(@Param('id') id: string): Promise<void> {
        return this.usersService.remove(id)
    }
}
```

## Security Best Practices

- **Hash passwords** using a strong algorithm (e.g., bcrypt with cost factor 10-12, argon2, or scrypt) — never store plaintext passwords
- **JWT secrets** must be at least 256 bits and stored in environment variables
- **Access tokens** should be short-lived (15-30 minutes)
- **Refresh tokens** should be stored securely and rotated on use
- **Rate limit** authentication endpoints to prevent brute force attacks
- **Never log** tokens, passwords, or authentication payloads
- **Invalidate sessions** on password change
