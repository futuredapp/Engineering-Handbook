# New project checklist

## 1. Create new repository

- [ ] Open [Android project template](https://github.com/futuredapp/android-project-template-compose) and create a new repository by **Use this template** button.
    - Set repository name. Preferred repository name is *[productname]-android*, where *[productname]* includes only name of the product, without client name.
    - Set owner to `futured.app`

## 2. Setup repository

- [ ] Create branch `develop` from `main`.
- [ ] Set `develop` as a **default branch** in *Settings  / General*.
- [ ] Disable **Wiki**, **Issues**, **Projects** features. We do not use them.
- [ ] Enable **Automatically delete head branches** so there is no need to delete branches manually after each PR merge.
- [ ] Add branch ruleset with name `develop|main`
    - Set targets as shown in the following screenshot:

![GitHub target branches](../general/attachments/GitHub_target_branches.png)

- Set required approvals

![GitHub target branches](../general/attachments/GitHub_required_approvals.png)

- Set status checks

![GitHub target branches](attachments/GitHub_status_checks.png)

> NOTE: Status checks (GitHub Actions, Danger, etc.) become visible after first created pull request. Do not forget to set them afterwards.

## 2. Setup project

- [ ] Load project in Android Studio: *New* / *Project from Version Control*
- [ ] Create new branch `feature/project-setup`
    - Update `README.md`
    - Update `applicationId`, `compile/target/minSdk`, ... in `ProjectSettings.kt`
    - Change packages from `app.futured.androidprojecttemplate` to real packages based on `applicationId`:
        - Change Android Studio project preview from `Android` to `Project`.
        - Find your package, typically `app/main/androidprojecttemplate`.
        - Refactor / Rename.
            - Android Studio won't be able to refactor everything. The rest needs to be refactored manually. Search and replace `androidprojecttemplate` by new part of `applicationId` manually.
            - Sometimes Android Studio keeps empty folder of old package name. You can remove it manually.
        - To check run: `./gradlew --continue lintCheck` and then `./gradlew assembleDevDebug`
    - Commit changes and create first PR to `develop`.
    - If PR was sucessfull set *Set status checks* in [Setup repository](https://github.com/futuredapp/Engineering-Handbook/edit/main/android/new-project.md#2-setup-repository)


## 2. Setup Firebase

- [ ] Create 2 new Firebase projects. First for: `Debug` + `Enterprise` and second for `Prod`. Or access an existing one from the client.
- [ ] If you created 2 new Firebase projects from scratch, continue with this setup or individually if you have Firebase from the client:
    - Debug + Enterprise
        - Add Android app for `debug` and `enterprise` and set SHA-1 from debug keystore.
    - Prod
        - Add Android app for `prod`
        - Set Project Settings / Environment type to `Production`
        - SHA-1: you will probably be using [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756?hl=en) by Google, so the SHA-1 can be filled in later

> Tips:  
> Get SHA-1 for `debug` keystore: `keytool -list -v -keystore debug.jks -alias androiddebugkey -storepass android`  
> To create different package name with suffix, add buildTypes/applicationIdSuffix to gradle for all build types.  
> If you want to generate release/upload keystore right now see [Generate an upload key and keystore](https://developer.android.com/studio/publish/app-signing#generate-key) and put password, alias and keystore to Bitwarden.

> Additional info:  
> The Google Cloud project is created automatically with the created Firebase project.


### App distribution

- [ ] Select `Debug + Enterprise` project and click on `Get started` button.

- [ ] Add Tester groups `Futured QA` and `Devs`. Create invite link and send it to the Slack channel.

- [ ] Authorizing upload to Firebase. There are two ways of authorizing upload to Firebase.
    - By Service account JSON, which is a **preferred way**
        1. Go to Google Cloud Console and find your project by name
        2. Go to APIs & Services / Credentials / Create Credentials / Service Account
            - Set service account name (i.e.:Firebase App Distribution) and set description
            - Set role: `Firebase App Distribution Admin`
        3. Create new key with type `JSON`: click on new created service account / Keys / Add key
        4. Create new repository secret `APP_DISTRIBUTION_SERVICE_ACCOUNT` with the JSON content
    - By using `FIREBASE_TOKEN`, which can be generated using Firebase CLI

> Make sure that you have enabled google services plugin, otherwise the plugin won't know the id of the app

## 3. Configure GitHub Actions

- [ ] If you created a new project using [Android project template](https://github.com/futuredapp/android-project-template-compose), the GitHub Actions workflow files are already present in your repository under `.github/workflows` folder. If not, copy them over from [here](https://github.com/futuredapp/android-project-template-compose/tree/main/.github/workflows)).
- [ ] In **Settings** tab of your repository, go to **Secrets** and configure following secrets:
    1. `DANGER_GITHUB_API_TOKEN`
    2. `SLACK_WEB_HOOK`
    3. `APP_DISTRIBUTION_SERVICE_ACCOUNT`

- [ ] For each workflow configuration file, configure the environment variables marked with `TODO` comment:
    1. `SLACK_CHANNEL`
    2. `APP_DISTRIBUTION_GROUPS`

- [ ] Make a pull request from `feature/PROJ-1-setup-project` to `develop` to ensure that GitHub Actions work properly

## 4. Enjoy!
