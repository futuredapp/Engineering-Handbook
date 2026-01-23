# Methodology

Our delivery methodology follows a quality-first approach, ensuring that all builds are thoroughly tested internally before reaching clients.

## General Delivery Workflow

!!! info "Project-specific workflows"

    The workflow described below is typical for most projects. However, the actual delivery process may vary from project to project based on specific project requirements and client needs.

The standard delivery process consists of three main stages:

1. **Internal QA Testing** - Each feature build is distributed to internal QA team for testing
2. **Client Distribution** - Only after QA approval and when milestone is reached, milestone build is published to client testing groups for UAT
3. **Production Release** - After client approval, release build is produced and tested (typically by internal QA), then released to App Store and Google Play

This multi-stage approach ensures that clients receive stable, tested builds and reduces the risk of exposing critical issues to external stakeholders. Production releases only occur after successful completion of all internal and client testing cycles.

### Complete Delivery Workflow

The diagram below shows the complete delivery process from development through to production release:

```
Development Done (PR merged to develop)
  │
  │ (Nightly build succeeds) ← See Nightly Builds documentation
  ↓
Testing (Internal QA)
  │
  │ (Internal QA testing complete)
  ↓
Internal QA Done
  │
  │ (Feature complete, continues to next feature)
  │
  ↓
[Repeats for each feature until milestone is reached]
  │
  ↓
Milestone Reached
  │
  │ (Milestone build published to client testing group)
  ↓
Client Testing
  │
  │ (Client approval/QA process)
  ├─→ Issues Found? ──→ (Loop back to Development)
  │
  ↓
Client Approval
  │
  │ (Milestone approved, continues)
  | or
  │ (Release build produced) ← See platform-specific build documentation
  ↓
Release Testing (Internal QA)
  │
  │ (Release testing complete)
  ↓
Production Release
  ├─→ Google Play (Android)
  └─→ App Store (iOS)
```

**Key Process Points:**

- **Nightly Build** → Automated daily builds with Jira integration (see [Nightly Builds](10_nightly_builds.md))
- **Internal QA** → Futured QA team tests each feature after development
- **Feature Loop** → Process repeats for each feature, accumulating until milestone is complete
- **Client Testing** → Client stakeholders test the complete milestone build and perform UAT
- **Release Testing** → Final testing on release build before store deployment (typically internal QA)
- **Production Release** → Final deployment to public app stores after release testing is complete

**Distribution Channels:**

- Android: [Firebase App Distribution](../android/project_setup/40_firebase.md) for internal QA and client testing groups
- iOS: [TestFlight](../ios/ios_builds.md) with internal and external testing groups

## Distribution Platforms

We use platform-specific distribution channels to deliver builds to both QA and client testing groups:

### Android: Firebase App Distribution

- **Internal QA Group**: Dedicated testing group for Futured QA engineers
- **Client Testing Group**: Separate group for client organization testers
- **Invitation Method**: We prefer using invitation links that clients can share within their organization
  - Clients use the invitation link to add their team members
  - This approach gives clients control over who has access to builds
  - See [Firebase Setup](../android/project_setup/40_firebase.md) for detailed configuration

### iOS: TestFlight

- **Internal Testing**: Dedicated internal testing group for QA engineers
- **External Testing**: Separate external testing group for client testers
- **Invitation Method**: Testers are invited via email or public link
  - Internal testers can be added immediately (up to 100 testers)
  - External testers require App Store Connect review for first build
  - See [iOS Builds](../ios/ios_builds.md) for more details

## Best Practices

### Build Distribution Timing

- **Nightly Builds**: Automated builds run daily (typically at night) and are automatically distributed to internal QA
- **Client Builds**: Distributed manually after QA approval, typically aligned with sprint cycles or client milestones
- **Hotfix or Expedited Builds**: Expedited process for critical fixes or quick feedback loop, may skip nightly cycle with proper testing

## Related Processes

- [Nightly Builds](10_nightly_builds.md) - Automated nightly build and distribution process
- [Android Build Distribution](../android/development/20_build_distrubution.md) - Android-specific distribution setup
- [iOS Builds](../ios/ios_builds.md) - iOS build types and TestFlight workflows
