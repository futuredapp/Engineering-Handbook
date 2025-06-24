# Build distribution

To distribute test builds among QA and our clients, we use Firebase App Distribution service. So far, a new Android build is created after each merge into the "develop" branch in project repositories. These builds are usually marked as **"Enterprise"** builds. Configuration of the target API environment may vary, but usually, it uses Staging or Dev variants. The main difference from **DEV** builds is that this build is not debuggable, and R8/ProGuard minification and obfuscation are enabled during the build process. The **Enterprise** build should behave from a security and performance perspective just like the **Release** build that will be deployed to Google Play. **Enterprise** builds should have fully configured analytics and Crashlytics services as there is no other way to investigate possible issues found during the testing phase.

## QA builds

Generally speaking, QA builds are the same builds as those provided to clients, except that QA has access to all builds as the QA group is automatically assigned to all builds deployed from CI.

## Client builds

Clients are provided with builds approved by QA for further testing and validation.

## Installing test builds

There are two ways to install an app from Firebase App Distribution: either using the web interface or installing the Firebase App Tester app, which will be offered to the users once they access a test build from an Android mobile device.
