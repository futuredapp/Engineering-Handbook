# Firebase

Create a new Firebase project, or gain access to existing project from client.

## New project
!!! warning "Work in progress"

Create `dev` and `prod` project using **ops@futured.app** email, invite developers.

!!! question "Reasoning behind `dev` and `prod`"
    TBD.

!!! info "Firebase vs. Google Cloud"
    Each Firebase project comes with Google Cloud project. When working with 3rd party integrations which require you to integrate with Google Cloud, be aware of this and use existing Google Cloud project if possible.

    **Example:** For setting up Google Sheets API key for localizations, create this key in dev Firebase Google Cloud project. For setting up Google Play developer API in CI/CD, use production Firebase Google Cloud project.

## Existing project
!!! warning "Work in progress"

Invite **ops@futured.app** to existing project. If there's only one existing project, consider that `prod` and create a new `dev` project.

## App setup
!!! warning "Work in progress"

Add Firebase to your project using [official docs](https://firebase.google.com/docs/android/setup).

- `applicationId.debug` goes in Dev project
- `applicationId.enterprise` goes in Dev project
- `applicationId` goes in Prod project

## Features

Crashlytics and Firebase App Distribution is must-have.

### Crashlytics

Enable Firebase crashlytics for enterprise and production builds. Make sure to disable [mapping file upload](https://firebase.google.com/docs/crashlytics/get-deobfuscated-reports?platform=android#keep-obfuscated-build-variants) for debug builds:
```kotlin
buildTypes {
    getByName("debug") {
        configure<CrashlyticsExtension> {
            isMappingFileUploadEnabled = false
        }
    }
}
```

### Firebase App Distribution
!!! warning "Work in progress"
    1. set up for enterprise build (dev project)
    2. create groups
    3. service account instructions
