# Third-Party Integrations & APIs

This page documents common third-party services we integrate with and the patterns we follow for clean, maintainable integrations.

## Integration Architecture

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
graph TB
    subgraph App["Our Application"]
        API([NestJS API]):::app
        IntLayer{{Integration Layer<br/>Dedicated Modules}}:::integration
    end

    subgraph Auth["Authentication"]
        Firebase([Firebase Auth]):::auth
        Google([Google OAuth]):::auth
        Apple([Apple Sign-In]):::auth
    end

    subgraph Comms["Communication"]
        Email([Email Service<br/>Resend / OneSignal]):::comms
        FCM([Firebase Cloud Messaging<br/>Push Notifications]):::comms
        Slack([Slack API<br/>Alerts & Notifications]):::comms
    end

    subgraph Store["Storage & Data"]
        CloudStorage[(Cloud Storage<br/>GCS / Spaces / S3)]:::storage
        DB[(Database)]:::storage
    end

    subgraph Pay["Payments"]
        Stripe([Stripe]):::payments
        GoPay([GoPay]):::payments
    end

    subgraph Mon["Monitoring"]
        Sentry([Sentry<br/>Error Tracking]):::monitoring
        Analytics([Google Analytics]):::monitoring
    end

    API --> IntLayer
    IntLayer --> Firebase
    IntLayer --> Google
    IntLayer --> Apple
    IntLayer --> Email
    IntLayer --> FCM
    IntLayer --> Slack
    IntLayer --> CloudStorage
    IntLayer --> Stripe
    IntLayer --> GoPay
    API --> Sentry
    API --> Analytics
    API --> DB

    classDef app fill:#3b82f6,stroke:#1e40af,color:#fff
    classDef integration fill:#8b5cf6,stroke:#5b21b6,color:#fff
    classDef auth fill:#f59e0b,stroke:#b45309,color:#fff
    classDef comms fill:#06b6d4,stroke:#0e7490,color:#fff
    classDef storage fill:#059669,stroke:#065f46,color:#fff
    classDef payments fill:#e11d48,stroke:#9f1239,color:#fff
    classDef monitoring fill:#22c55e,stroke:#15803d,color:#fff
```

## Integration Patterns

### Wrapping External Services

Always wrap third-party services in a dedicated NestJS module. This isolates external dependencies and makes them easy to swap or mock.

```typescript
// integrations/email/email.module.ts
@Module({
    providers: [EmailService],
    exports: [EmailService],
})
export class EmailModule {}

// integrations/email/email.service.ts
@Injectable()
export class EmailService {
    constructor(private readonly configService: ConfigService) {}

    async sendEmail(to: string, template: string, data: Record<string, unknown>): Promise<void> {
        // Provider-specific implementation
    }
}
```

**Why wrap:**

- Feature modules depend on `EmailService`, not on a specific provider
- Switching from one email provider to another only changes one file
- Easy to mock in tests

### Integration Flow

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
sequenceDiagram
    participant App as Application Code
    participant Module as Integration Module
    participant Provider as External Provider

    App->>Module: emailService.sendWelcome(user)
    activate Module
    Module->>Module: Build template, validate data
    Module->>Provider: POST /api/send (API call)
    activate Provider
    Provider-->>Module: 200 OK / Error
    deactivate Provider
    Module->>Module: Log result, handle errors
    Module-->>App: Success / throw exception
    deactivate Module
```

### Configuration

All integration credentials are managed as environment variables, never hardcoded:

```typescript
// integrations/storage/storage.service.ts
@Injectable()
export class StorageService {
    private readonly bucket: string

    constructor(private readonly configService: ConfigService) {
        this.bucket = this.configService.getOrThrow<string>('STORAGE_BUCKET')
    }
}
```

## Common Integrations

### Email

- **Resend** — Transactional emails, our primary email provider
- **OneSignal** — Email and push notification platform

Emails are sent via API, not SMTP. We use templates managed in the email provider's dashboard or in-code with MJML/HTML.

### File Storage

- **GCP Cloud Storage** — For GCP-hosted projects
- **Digital Ocean Spaces** — S3-compatible, for DO-hosted projects
- **AWS S3** — When the client's infrastructure is on AWS

```typescript
// Common pattern: generate signed upload URLs
async getUploadUrl(fileName: string): Promise<string> {
    const key = `uploads/${Date.now()}-${fileName}`
    return this.storage.getSignedUrl(key, {
        action: 'write',
        expires: Date.now() + 15 * 60 * 1000, // 15 minutes
        contentType: 'application/octet-stream',
    })
}
```

### Payments

- **Stripe** — Primary payment processor
- **GoPay** — Used for Czech/Slovak market projects

<!-- TODO: Replace Mermaid diagram with a custom-designed SVG/image -->
```mermaid
sequenceDiagram
    participant Client as Frontend
    participant API as Our API
    participant Stripe as Stripe

    Client->>API: Create payment intent
    activate API
    API->>Stripe: POST /payment_intents
    activate Stripe
    Stripe-->>API: Payment intent + client_secret
    deactivate Stripe
    API-->>Client: client_secret
    deactivate API

    Client->>Stripe: Confirm payment (card details)
    activate Stripe
    Stripe-->>Client: Payment result
    deactivate Stripe

    Note over Stripe,API: Asynchronous webhook callback
    Stripe->>API: Webhook: payment_intent.succeeded
    activate API
    API->>API: Verify signature, update order
    API-->>Stripe: 200 OK
    deactivate API
```

For payments, we always:

1. Use webhooks to confirm payment status (never trust client-side callbacks alone)
2. Store payment events in our database for auditability
3. Implement idempotency keys for retry safety

### Authentication Providers

- **Google OAuth** — Most common social login
- **Apple Sign-In** — Required for iOS apps with social login
- **Firebase Auth** — Managed authentication for projects that don't need custom auth logic

See [Authentication & Authorization](../security/00_auth.md) for implementation details.

### Analytics & Monitoring

- **Sentry** — Error tracking and performance monitoring (see [Monitoring](../deployment/20_monitoring.md))
- **Google Analytics** — Web analytics (frontend)
- **Mixpanel / PostHog** — Product analytics when needed

### Communication

- **Slack API** — Build notifications, deployment alerts, CI/CD integration
- **Firebase Cloud Messaging (FCM)** — Push notifications for mobile and web apps

## Integration Checklist

When adding a new third-party integration:

- [ ] Create a dedicated module in `src/integrations/<service>/`
- [ ] Add required environment variables to `docker-compose.dist.yml` and `.env.example`
- [ ] Document the integration in the project README
- [ ] Add error handling for API failures (retries, circuit breakers for critical paths)
- [ ] Write integration tests with mocked responses
- [ ] Verify the service has acceptable uptime SLAs for production use
