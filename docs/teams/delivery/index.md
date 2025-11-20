# Delivery

Welcome to the Delivery section of the Futured Engineering Handbook. This section covers our build distribution process from an engineering perspective, including testing, QA workflows, and client delivery across all platforms.

Our delivery process ensures that builds are thoroughly tested internally before reaching clients, maintaining high quality standards while providing efficient feedback loops.

## Overview

The delivery process encompasses:

- **Build Distribution**: Automated and manual distribution of builds to QA teams and clients
- **Testing Workflows**: Internal QA testing before client distribution
- **Platform Integration**: Firebase App Distribution for Android, TestFlight for iOS
- **Automation**: Nightly builds with automated Jira integration

## Key Sections

### Fundamentals

- [Methodology](00_methodology.md) - Our general delivery methodology: QA-first testing, client distribution workflows, and platform-specific distribution channels
- [Nightly Builds](10_nightly_builds.md) - Automated nightly build process with GitHub Actions and Jira integration for efficient continuous delivery

## Related Documentation

For platform-specific build and release processes, see:

- [Android Build Distribution](../android/development/20_build_distrubution.md) - Android-specific Firebase App Distribution setup and workflows
- [iOS Builds](../ios/ios_builds.md) - iOS build types and TestFlight distribution
- [Firebase Setup](../android/project_setup/30_firebase.md) - Detailed Firebase App Distribution configuration
- [Android Release Process](../android/release/30_release_process.md) - Complete Android release workflow
- [iOS Release Process](../ios/ios_release.md) - Complete iOS release workflow

## Getting Started

If you're new to the delivery process:

1. Start with [Methodology](00_methodology.md) to understand our QA-to-client workflow
2. Review [Nightly Builds](10_nightly_builds.md) to understand automated build distribution
3. Check platform-specific documentation for detailed setup instructions
