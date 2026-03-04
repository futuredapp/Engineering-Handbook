# Data Privacy & Compliance

Protecting user data is a core responsibility. This guide covers our practices for handling personal data, complying with GDPR, and building privacy-aware applications.

## GDPR Overview

The General Data Protection Regulation (GDPR) applies to all projects that process personal data of EU/EEA residents — which includes most of our projects.

### Key Principles

- **Purpose limitation** — Collect data only for a specific, stated purpose
- **Data minimization** — Don't collect more data than needed
- **Storage limitation** — Don't keep data longer than necessary
- **Integrity and confidentiality** — Protect data from unauthorized access

### User Rights

Our applications must support these user rights where applicable:

| Right | What it means | Implementation |
|---|---|---|
| Right to access | User can request a copy of their data | Export endpoint / admin panel |
| Right to rectification | User can correct inaccurate data | Edit profile / admin update |
| Right to erasure | User can request deletion of their data | Account deletion flow |
| Right to data portability | User can get their data in a machine-readable format | JSON/CSV export |

## Data Classification

Classify data in your application to apply appropriate protections:

| Category | Examples | Handling |
|---|---|---|
| **PII** (Personally Identifiable Information) | Email, name, phone, address | Encrypt at rest, limit access, log access |
| **Sensitive PII** | Passwords, government IDs, health data | Encrypt at rest, never log, strict access control |
| **Non-personal** | Aggregated analytics, system logs (without PII) | Standard handling |

## Implementation Guidelines

### Data Collection

```typescript
// Only collect what you need
export class CreateUserDto {
    @IsEmail()
    email: string   // Needed for authentication

    @IsString()
    name: string    // Needed for display

    // Don't collect: date of birth, phone number, address
    // unless the business requirement explicitly needs it
}
```

### Data Storage

- **Encrypt sensitive fields** at the database level when required
- **Hash passwords** — never store plaintext (use bcrypt)
- **Separate PII** from non-sensitive data where possible (e.g., separate user profile table)

### Data Retention

Define retention periods for each data type:

```typescript
// Example: Scheduled cleanup of old data
@Cron(CronExpression.EVERY_DAY_AT_MIDNIGHT)
async cleanupExpiredData() {
    // Delete unverified accounts older than 30 days
    await this.usersService.deleteUnverified(30)

    // Delete audit logs older than 1 year
    await this.auditService.deleteOlderThan(365)
}
```

**Common retention periods:**

| Data type | Retention | Reason |
|---|---|---|
| Active user accounts | While account is active | Business need |
| Inactive accounts | 2 years after last login | Storage limitation |
| Audit logs | 1 year | Security monitoring |
| Session data | Until session expires | Functional need |
| Deleted user data | 30 days (soft delete), then purge | Recovery period |

### Account Deletion

When a user requests account deletion:

1. **Soft delete** the account (mark as deleted, anonymize PII)
2. **Retain** data required by law (e.g., invoices for tax purposes)
3. **Hard delete** after the retention period expires
4. **Cascade** deletion to related entities (user's content, preferences, etc.)

```typescript
async deleteUserAccount(userId: string): Promise<void> {
    const user = await this.usersService.findOne(userId)

    // Anonymize PII
    await this.usersService.update(userId, {
        email: `deleted-${userId}@anonymized.local`,
        name: 'Deleted User',
        deletedAt: new Date(),
    })

    // Remove from external services
    await this.emailService.removeSubscriber(user.email)
    await this.analyticsService.anonymizeUser(userId)
}
```

## Consent Management

- **Cookie consent** — Use a cookie consent banner for analytics and marketing cookies
- **Terms of Service** — Track user acceptance with a timestamp
- **Marketing consent** — Store explicit opt-in/opt-out per communication channel

```typescript
@Entity()
export class UserConsent {
    @PrimaryGeneratedColumn('uuid')
    id: string

    @ManyToOne(() => User)
    user: User

    @Column()
    consentType: string // 'marketing_email', 'analytics', 'terms_of_service'

    @Column()
    granted: boolean

    @CreateDateColumn()
    grantedAt: Date
}
```

## Third-Party Data Sharing

- **Never share PII** with third parties without explicit user consent
- **Audit third-party services** for GDPR compliance before integration
- **Data Processing Agreements (DPA)** must be in place with all processors
- **Don't paste user data** into AI tools, chatbots, or external services without anonymization

## Developer Checklist

When building features that handle user data:

- [ ] Only collect data that is necessary for the feature
- [ ] Document what data is collected and why
- [ ] Implement data export functionality for user access requests
- [ ] Support account deletion with proper data anonymization
- [ ] Set up data retention policies and automated cleanup
- [ ] Never log PII (emails, names, tokens) in application logs
- [ ] Ensure third-party services have DPAs in place
- [ ] Include privacy considerations in code reviews
