# Project setup
Let's set up a project before start coding.

1. Load project in Android Studio: *New* / *Project from Version Control*.
2. Create new branch `feature/project-setup` from `develop`.
3. Update the project description in `README.md`.

### KMP

This is relatively straightforward, as most of the setup is handled by the init script:

1. Follow the instructions in the repository, which include running the [init script](https://github.com/futuredapp/kmp-futured-template?tab=readme-ov-file#initial-script) that will set up the application ID, package naming, and other settings.

### Android only

1. Update `applicationId`, `compile/target/minSdk`, ... in [`ProjectSettings.kt`](https://github.com/futuredapp/android-project-template-compose/blob/9948420d4ab3f1fbb8cb45a466e4603f8ed841d4/buildSrc/src/main/kotlin/ProjectSettings.kt)
2. Change packages from `app.futured.androidprojecttemplate` to real packages based on `applicationId`:
    - Change project preview in Android Studio from `Android` to `Project`.
    - Find your package, typically `app/main/androidprojecttemplate`.
    - Right click and select Refactor / Rename.
        - Android Studio won't be able to refactor everything. The rest needs to be refactored manually. Search and replace `androidprojecttemplate` by new package name manually.
        - Android Studio sometimes keeps empty folders of old package name. Search them and remove manually.
          - To check if refactoring was successful, run: 
            - `./gradlew --continue lintCheck`
            - `./gradlew assembleDevDebug`
3. Commit changes and create first PR to `develop`.
4. If PR was successful *Status checks* will be visible now. See *Require status checks to pass* in [Branch Rulesets / Configuration](./10_github.md#configuration) section.
