# Project setup
Let's set up a project before starting to code.

1. Load the project in Android Studio: *New* / *Project from Version Control*.
2. Create a new branch `feature/project-setup` from `develop`.
3. Update the project description in `README.md`.

### KMP

This is relatively straightforward, as most of the setup is handled by the init script:

1. Follow the instructions in the repository, which include running the [init script](https://github.com/futuredapp/kmp-futured-template?tab=readme-ov-file#initial-script) that will set up the application ID, package naming, and other settings.
2. Update the [`ProjectSettings.kt`](https://github.com/futuredapp/kmp-futured-template/blob/develop/buildSrc/src/main/kotlin/app/futured/kmptemplate/gradle/configuration/ProjectSettings.kt) and [`ProductFlavors.kt`](https://github.com/futuredapp/kmp-futured-template/blob/develop/buildSrc/src/main/kotlin/app/futured/kmptemplate/gradle/configuration/ProductFlavors.kt) to your specific needs.

### Android only

1. Update `applicationId`, `compile/target/minSdk`, etc. in [`ProjectSettings.kt`](https://github.com/futuredapp/android-project-template-compose/blob/9948420d4ab3f1fbb8cb45a466e4603f8ed841d4/buildSrc/src/main/kotlin/ProjectSettings.kt)
2. Change packages from `app.futured.androidprojecttemplate` to real packages based on `applicationId`:
    - Change the project preview in Android Studio from `Android` to `Project`.
    - Find your package, typically `app/main/androidprojecttemplate`.
    - Right-click and select Refactor / Rename.
        - Android Studio won't be able to refactor everything. The rest needs to be refactored manually. Search and replace `androidprojecttemplate` with the new package name manually.
        - Android Studio sometimes keeps empty folders of the old package name. Search for them and remove them manually.
          - To check if refactoring was successful, run: 
            - `./gradlew --continue lintCheck`
            - `./gradlew assembleDevDebug`
3. Commit changes and create the first PR to `develop`.
4. If the PR was successful, *Status checks* will be visible now. See *Require status checks to pass* in the [Branch Rulesets / Configuration](./20_github.md#configuration) section.
