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

For applications that don't use GraphQL, use standard HTTP exceptions with structured responses:

```typescript
// REST API error handling
import { 
  HttpException, 
  HttpStatus 
} from '@nestjs/common';

// Custom error response structure
interface ErrorResponse {
  statusCode: number;
  message: string;
  error: string;
  timestamp: string;
  path: string;
  details?: any;
}

// Usage examples for REST APIs
throw new HttpException({
  statusCode: HttpStatus.NOT_FOUND,
  message: 'User not found',
  error: 'Not Found',
  timestamp: new Date().toISOString(),
  path: '/api/users/123',
  details: {
    userId: '123',
    resourceType: 'User'
  }
}, HttpStatus.NOT_FOUND);

throw new HttpException({
  statusCode: HttpStatus.BAD_REQUEST,
  message: 'Validation failed',
  error: 'Bad Request',
  timestamp: new Date().toISOString(),
  path: '/api/users',
  details: {
    field: 'email',
    value: 'invalid-email',
    constraint: 'email_format'
  }
}, HttpStatus.BAD_REQUEST);

throw new HttpException({
  statusCode: HttpStatus.CONFLICT,
  message: 'User already exists',
  error: 'Conflict',
  timestamp: new Date().toISOString(),
  path: '/api/users',
  details: {
    resourceId: 'user@example.com',
    resourceType: 'User',
    constraint: 'email_unique'
  }
}, HttpStatus.CONFLICT);

```


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

    const errorResponse = {
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      message: exception.message,
      error: exception.getResponse(),
    };

    // Log the error
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
// Always use try-catch with async operations
async createUser(userData: CreateUserDto): Promise<User> {
  try {
    const user = await this.userRepository.create(userData);
    return user;
  } catch (error) {
    this.logger.error('Failed to create user', {
      error: error.message,
      userData,
      stack: error.stack,
    });
    
    if (error.code === '23505') { // PostgreSQL unique constraint
      throw new ConflictException({
        message: 'User already exists',
        error: 'CONFLICT',
        resourceId: userData.email,
        resourceType: 'User',
        constraint: 'email_unique'
      });
    }
    
    throw new InternalServerErrorException({
      message: 'Internal server error',
      error: 'INTERNAL_ERROR',
      operation: 'create_user',
      details: error.message
    });
  }
}
```

---

## Logging

### 1. Winston Configuration

```typescript
// config/winston.config.ts
import { WinstonModule } from 'nest-winston';
import * as winston from 'winston';

export const winstonConfig = WinstonModule.createLogger({
  transports: [
    // Console transport for development
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.colorize(),
        winston.format.simple()
      ),
    }),
    
    // Google Cloud Logging
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.json()
      ),
    }),
  ],
  level: process.env.LOG_LEVEL || 'info',
});
```

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

### 5. Future: Elasticsearch Integration

```typescript
// config/elasticsearch-logger.config.ts
import { ElasticsearchTransport } from 'winston-elasticsearch';

const elasticsearchTransport = new ElasticsearchTransport({
  level: 'info',
  clientOpts: {
    node: process.env.ELASTICSEARCH_URL,
    auth: {
      username: process.env.ELASTICSEARCH_USERNAME,
      password: process.env.ELASTICSEARCH_PASSWORD,
    },
  },
  indexPrefix: 'logs',
  ensureMappingTemplate: true,
  mappingTemplate: {
    index_patterns: ['logs-*'],
    settings: {
      number_of_shards: 1,
      number_of_replicas: 0,
    },
  },
});

// Add to winston config
transports: [
  // ... existing transports
  elasticsearchTransport,
]
```

---

## Best Practices

### Error Handling
- Use built-in GraphQL exceptions for consistent error responses
- Implement global exception filters for centralized error handling
- Handle async errors with try-catch blocks
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