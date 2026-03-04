# Monitoring & Alerting

This guide covers the baseline observability practices we use across projects. Different projects have different monitoring needs — Sentry and structured logging are our standard everywhere, while more advanced tooling (APM, custom dashboards, detailed alerting) is set up per project based on its requirements and is not documented here.

## Error Tracking

### Sentry

Sentry is our primary error tracking tool for both backend and frontend applications. It's present in almost every project.

#### Backend Setup (NestJS)

```typescript
// main.ts
import * as Sentry from '@sentry/node'

Sentry.init({
    dsn: process.env.SENTRY_DSN,
    environment: process.env.NODE_ENV,
    tracesSampleRate: process.env.NODE_ENV === 'production' ? 0.2 : 1.0,
})
```

```typescript
// common/filters/sentry-exception.filter.ts
@Catch()
export class SentryExceptionFilter implements ExceptionFilter {
    catch(exception: unknown, host: ArgumentsHost) {
        Sentry.captureException(exception)
        // ... pass to default exception handler
    }
}
```

#### Frontend Setup (Vue / Nuxt)

```typescript
// plugins/sentry.client.ts
import * as Sentry from '@sentry/vue'

export default defineNuxtPlugin((nuxtApp) => {
    Sentry.init({
        app: nuxtApp.vueApp,
        dsn: useRuntimeConfig().public.sentryDsn,
        environment: useRuntimeConfig().public.environment,
        tracesSampleRate: 0.2,
    })
})
```

#### Best Practices

- Set meaningful `environment` and `release` tags
- Add user context when available (`Sentry.setUser({ id, email })`)
- Use breadcrumbs to trace the sequence of events leading to an error
- Filter out expected errors (4xx responses, validation errors) to reduce noise

## Logging

We use **Pino** for structured JSON logging. Pino is fast, low-overhead, and works well with NestJS.

### Setup

```typescript
// main.ts
import { LoggerModule } from 'nestjs-pino'

@Module({
    imports: [
        LoggerModule.forRoot({
            pinoHttp: {
                level: process.env.LOG_LEVEL || 'info',
                transport: process.env.NODE_ENV !== 'production'
                    ? { target: 'pino-pretty' }
                    : undefined,
            },
        }),
    ],
})
export class AppModule {}
```

### Key Guidelines

- Use structured JSON logging in production (Pino does this by default)
- Include correlation/request IDs for tracing requests across services
- Log at appropriate levels: `error` for failures, `warn` for degraded behavior, `info` for significant events, `debug` for development
- Never log sensitive data (passwords, tokens, PII)
- Use `pino-pretty` in development for readable output

### Log Aggregation

- **GCP projects**: Logs are automatically collected by Google Cloud Logging when output as JSON to stdout
- **DO projects**: Use Digital Ocean's built-in log viewer or forward logs to a centralized service

See [Error Handling & Logging](../coding_standards/40_error_handling.md) for more patterns on structured logging and error context.

## Health Checks

Every application exposes a health check endpoint:

```typescript
// health/health.controller.ts
@Controller('health')
export class HealthController {
    constructor(
        private health: HealthCheckService,
        private db: TypeOrmHealthIndicator,
    ) {}

    @Get()
    check() {
        return this.health.check([
            () => this.db.pingCheck('database'),
        ])
    }
}
```

Health checks are used by:

- **Load balancers** to route traffic to healthy instances
- **Container orchestrators** to restart unhealthy containers
- **Uptime monitors** to detect outages

## Alerting

### Alert Channels

- **Slack** — Primary channel for all alerts
- **Email** — Backup for critical alerts

### What to Alert On

- Application errors spiking above baseline (Sentry alerts)
- Health check failures
- High response time sustained over several minutes
- Resource usage thresholds (CPU, memory)

### Avoiding Alert Fatigue

- Only alert on actionable conditions — if no one needs to act, it's a log, not an alert
- Group related errors in Sentry (deduplication)
- Set reasonable thresholds — occasional 500 errors happen, only alert on sustained issues
- Review and tune alert rules periodically
