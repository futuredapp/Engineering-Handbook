# First Steps

## Creating a Repository
Create a new **private** repository by using one of the project templates, depending on the project type:

- [futuredapp/kmp-futured-template](https://github.com/futuredapp/kmp-futured-template) for Kotlin Multiplatform (KMP) projects
- [futuredapp/android-project-template-compose](https://github.com/futuredapp/android-project-template-compose) for Android-only projects

### Repository Naming
When creating a new repository, follow the naming convention:

- KMP: `<project-name>-kmp`
- Android-only: `<project-name>-android`

## Project Setup

### KMP

This is relatively straightforward, as most of the setup is handled by the init script:

1. Update the project description in `README.md`.
2. Follow the instructions in the repository, which include running the [init script](https://github.com/futuredapp/kmp-futured-template?tab=readme-ov-file#initial-script) that will set up the application ID, package naming, and other settings.

### Android-only

A more manual approach is needed:

1. Update the project description in `README.md`.
2. Update the project name.
3. Update the `applicationId` in [`ProjectSettings.kt`](https://github.com/futuredapp/android-project-template-compose/blob/main/buildSrc/src/main/kotlin/ProjectSettings.kt).
4. Rename packages from `app.futured.androidprojecttemplate` to the real package based on the `applicationId`.
