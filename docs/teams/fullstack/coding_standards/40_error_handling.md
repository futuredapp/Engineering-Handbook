# Error Handling & Logging

This guide covers best practices for error handling and logging in our NestJS applications.

## Error Handling

### 1. Use GraphQL Exceptions

```typescript
// Use built-in GraphQL exceptions
import { 
  BadRequestException, 
  NotFoundException, 
  UnauthorizedException,
  ForbiddenException,
  ConflictException,
  InternalServerErrorException 
} from '@nestjs/common';

// Usage examples with context
throw new NotFoundException({
  message: 'User not found',
  error: 'NOT_FOUND',
  resourceId: userId,
  resourceType: 'User'
});

throw new BadRequestException({
  message: 'Invalid input data',
  error: 'VALIDATION_ERROR',
  field: 'email',
  value: email
});

throw new UnauthorizedException({
  message: 'Authentication required',
  error: 'UNAUTHORIZED',
  action: 'access_user_profile'
});

throw new ForbiddenException({
  message: 'Insufficient permissions',
  error: 'FORBIDDEN',
  resourceId: userId,
  requiredPermission: 'ADMIN'
});

throw new ConflictException({
  message: 'Resource already exists',
  error: 'CONFLICT',
  resourceId: email,
  resourceType: 'User'
});

throw new InternalServerErrorException({
  message: 'Database connection failed',
  error: 'INTERNAL_ERROR',
  service: 'Database',
  operation: 'create_user'
});
```

### 2. REST API Error Handling (Non-GraphQL Apps)

For applications that don't use GraphQL, prefer a **centralized error catalog** instead of inlining error objects at every throw site. Define a base `ApplicationError`, an enum of error codes, and a catalog of static factory methods. This keeps message wording, error codes, and HTTP status mapped in one place.

**Define the base error and the code enum:**

```typescript
// errors/error-code.enum.ts
export enum ErrorCodeEnum {
  BAD_USER_INPUT = 'BAD_USER_INPUT',
  RESOURCE_NOT_FOUND = 'RESOURCE_NOT_FOUND',
  CONFLICT = 'CONFLICT',
  UNAUTHORIZED = 'UNAUTHORIZED',
  FORBIDDEN = 'FORBIDDEN',
  INTERNAL_ERROR = 'INTERNAL_ERROR',
}

// errors/application.error.ts
export class ApplicationError extends HttpException {
  constructor(
    message: string,
    public readonly errorCode: ErrorCodeEnum,
    httpStatus: HttpStatus,
  ) {
    super({ message, errorCode }, httpStatus);
  }
}
```

**Group reusable errors in a catalog with static factory methods:**

```typescript
// errors/common-errors.ts
export class CommonErrors {
  static InvalidInputData() {
    return new ApplicationError(
      'Request body does not match any of the expected types for this operation.',
      ErrorCodeEnum.BAD_USER_INPUT,
      HttpStatus.BAD_REQUEST,
    );
  }

  static UserNotFound(userId: string) {
    return new ApplicationError(
      `User with id "${userId}" was not found.`,
      ErrorCodeEnum.RESOURCE_NOT_FOUND,
      HttpStatus.NOT_FOUND,
    );
  }

  static EmailAlreadyTaken(email: string) {
    return new ApplicationError(
      `User with email "${email}" already exists.`,
      ErrorCodeEnum.CONFLICT,
      HttpStatus.CONFLICT,
    );
  }
}
```

**Throw sites stay one-liners:**

```typescript
throw CommonErrors.InvalidInputData();
throw CommonErrors.UserNotFound(userId);
throw CommonErrors.EmailAlreadyTaken(email);
```

**Why this beats inline `HttpException` literals:**

- One place to fix wording, error codes, or status mappings — no grepping the codebase when QA flags an inconsistent message
- Drift-free: identical errors at different call sites are guaranteed to stay identical
- Easier auditing: every error the API can return is enumerable in one file (great for QA, frontend, and i18n)
- Domain catalogs scale naturally — keep `CommonErrors` generic and add `UserErrors`, `OrderErrors`, etc. as the codebase grows

Ancillary fields like `timestamp` and `path` belong in the global exception filter (next section), **not at the throw site** — the filter sees the request and adds them to every response uniformly.


### 3. Global Exception Filter

```typescript
// filters/http-exception.filter.ts
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();
    const payload = exception.getResponse();
    const body = typeof payload === 'string'
      ? { message: payload }
      : (payload as Record<string, unknown>);

    const errorResponse = {
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      ...body, // spreads `message`, `errorCode`, etc. from ApplicationError
    };

    this.logger.error('HTTP Exception', {
      ...errorResponse,
      stack: exception.stack,
    });

    response.status(status).json(errorResponse);
  }
}
```

### 4. Validation Errors

```typescript
// pipes/validation.pipe.ts
@Injectable()
export class ValidationPipe extends DefaultValidationPipe {
  transform(value: any, metadata: ArgumentMetadata) {
    try {
      return super.transform(value, metadata);
    } catch (error) {
      if (error instanceof BadRequestException) {
        throw new BadRequestException({
          message: 'Validation failed',
          error: 'VALIDATION_ERROR',
          details: error.getResponse()
        });
      }
      throw error;
    }
  }
}
```

### 5. Async Error Handling

```typescript
// Catch only the errors you can translate into a domain error.
// Re-throw anything else — the global filter turns unknown errors into 500s uniformly.
async createUser(userData: CreateUserDto): Promise<User> {
  try {
    return await this.userRepository.create(userData);
  } catch (error) {
    this.logger.error('Failed to create user', {
      error: error.message,
      userData,
      stack: error.stack,
    });

    if (error.code === '23505') { // PostgreSQL unique constraint
      throw CommonErrors.EmailAlreadyTaken(userData.email);
    }

    throw error; // unknown failure — let the global filter handle it
  }
}
```

---

## Logging

### 1. Pino Configuration

We use Pino via `nestjs-pino` for structured logging. See [Monitoring & Alerting](../deployment/20_monitoring.md) for the setup.

### 2. Structured Logging

```typescript
// Always use structured logging with context
@Injectable()
export class UserService {
  constructor(private readonly logger: Logger) {}

  async findUser(id: string): Promise<User> {
    this.logger.log('Finding user', {
      userId: id,
      service: 'UserService',
      method: 'findUser',
    });

    try {
      const user = await this.userRepository.findById(id);
      
      this.logger.log('User found', {
        userId: id,
        userEmail: user.email,
        service: 'UserService',
        method: 'findUser',
      });
      
      return user;
    } catch (error) {
      this.logger.error('Failed to find user', {
        userId: id,
        error: error.message,
        service: 'UserService',
        method: 'findUser',
        stack: error.stack,
      });
      throw error;
    }
  }
}
```

### 3. Log Levels

```typescript
// Use appropriate log levels
logger.error('Critical error that needs immediate attention', { error, context });
logger.warn('Warning that should be monitored', { warning, context });
logger.info('General information about application flow', { info, context });
logger.debug('Detailed debugging information', { debug, context });
logger.verbose('Very detailed debugging information', { verbose, context });
```

### 4. Request Logging Interceptor

```typescript
// interceptors/logging.interceptor.ts
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  constructor(private readonly logger: Logger) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const { method, url, body, user } = request;
    const now = Date.now();

    this.logger.log('Incoming request', {
      method,
      url,
      body: this.sanitizeBody(body),
      userId: user?.id,
      userAgent: request.get('User-Agent'),
    });

    return next.handle().pipe(
      tap(() => {
        const responseTime = Date.now() - now;
        this.logger.log('Request completed', {
          method,
          url,
          responseTime: `${responseTime}ms`,
        });
      }),
      catchError((error) => {
        const responseTime = Date.now() - now;
        this.logger.error('Request failed', {
          method,
          url,
          responseTime: `${responseTime}ms`,
          error: error.message,
        });
        throw error;
      })
    );
  }

  private sanitizeBody(body: any): any {
    // Remove sensitive data from logs
    const sanitized = { ...body };
    delete sanitized.password;
    delete sanitized.token;
    return sanitized;
  }
}
```

---

## Best Practices

### Error Handling
- Centralize REST errors in an `ApplicationError` + `CommonErrors` catalog instead of inlining `HttpException` literals at throw sites
- Keep `path`, `timestamp`, and other request-scoped fields in the global exception filter, not at the throw site
- Use built-in GraphQL exceptions for consistent error responses
- Implement global exception filters for centralized error handling
- Handle async errors with try-catch blocks; only translate errors you understand and re-throw the rest
- Don't expose internal errors to clients
- Log all errors with sufficient context

### Logging
- Use structured logging with consistent fields
- Include relevant context (userId, requestId, service, method)
- Sanitize sensitive data before logging
- Use appropriate log levels
- Log request/response cycles for debugging
- Include stack traces for errors
- Use correlation IDs for request tracing

### Performance
- Avoid logging in hot paths
- Use async logging when possible
- Batch log entries for better performance
- Monitor log volume and storage costs  