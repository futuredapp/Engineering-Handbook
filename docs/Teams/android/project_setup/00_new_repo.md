# First steps

## Creating repository
Create new **private** repository by using one of project templates, depending on the project type:

- [futuredapp/kmp-futured-template](https://github.com/futuredapp/kmp-futured-template) for Kotlin Multiplatform (KMP) projects
- [futuredapp/android-project-template-compose](https://github.com/futuredapp/android-project-template-compose) for Android-only projects

### Repository naming
When creating a new repository, follow the naming convention:

- KMP: `<project-name>-kmp`
- Android-only: `<project-name>-android`

## Project Setup

### KMP

This is pretty easy, most of the stuff is handled by init script:

1. Update project description in `README.md`.
2. Follow instructions in repository, which include running [init script](https://github.com/futuredapp/kmp-futured-template?tab=readme-ov-file#initial-script) which will set up application ID, package naming and other settings.

### Android-only

More manual approach is needed:

1. Update project description in `README.md`.
2. Update project name.
3. Update `applicationId` in [`ProjectSettings.kt`](https://github.com/futuredapp/android-project-template-compose/blob/main/buildSrc/src/main/kotlin/ProjectSettings.kt).
4. Rename packages from `app.futured.androidprojecttemplate` to real package based on `applicationId`.
