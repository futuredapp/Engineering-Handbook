# Methodology

Our delivery methodology follows a quality-first approach, ensuring that all builds are thoroughly tested internally before reaching clients.

## General Delivery Workflow

The standard delivery process consists of two main stages:

1. **Internal QA Testing** - Build is distributed to internal QA team for testing
2. **Client Distribution** - Only after QA approval, build is published to client testing groups

This two-stage approach ensures that clients receive stable, tested builds and reduces the risk of exposing critical issues to external stakeholders.

## Distribution Platforms

We use platform-specific distribution channels to deliver builds to both QA and client testing groups:

### Android: Firebase App Distribution

- **Internal QA Group**: Dedicated testing group for Futured QA engineers
- **Client Testing Group**: Separate group for client organization testers
- **Invitation Method**: We prefer using invitation links that clients can share within their organization
  - Clients use the invitation link to add their team members
  - This approach gives clients control over who has access to builds
  - See [Firebase Setup](../android/project_setup/30_firebase.md) for detailed configuration

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
- **Hotfix Builds**: Expedited process for critical fixes, may skip nightly cycle with proper testing

## Related Processes

- [Nightly Builds](10_nightly_builds.md) - Automated nightly build and distribution process
- [Android Build Distribution](../android/development/20_build_distrubution.md) - Android-specific distribution setup
- [iOS Builds](../ios/ios_builds.md) - iOS build types and TestFlight workflows
