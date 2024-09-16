# New project checklist

## 1. Create new repository

- [ ] Open [Android project template](https://github.com/futuredapp/android-project-template-compose) and use it to create new repository. Preferred repo name is *[productname]-android*, where *[productname]* includes only name of the product, without client name.

- [ ] Change following in newly created repository:
    - Update Readme.md
    - Change project name to real project name
    - Change applicationId
    - Change packages from `com.thefuntasty.androidprojecttemplate` to real packages based on applicationId

- [ ] Create `develop` branch and set it as a default branch.

- [ ] Disable **Wiki**, **Issues**, **Projects** features we do not use.

- [ ] Enable **Automatically delete head branches** so there is no need to delete branches manually after each PR merge.

- [ ] Add branch ruleset `develop|main`
  - set targets as shown in the following screenshot:

![GitHub target branches](../general/attachments/GitHub_target_branches.png)

- Set required approvals

![GitHub target branches](../general/attachments/GitHub_required_approvals.png)

- Set status checks

![GitHub target branches](attachments/GitHub_status_checks.png)

> (NOTE: Status checks (GitHub Actions, Danger, etc.) become visible after first created pull request. Do not forget to set them afterwards.

- [ ] Create new branch `feature/PROJ-1-setup-project` where everything will be set up in next steps.

## 2. Setup Firebase

- [ ] Create new Firebase project or get access to an existing one from the client.

- [ ] Create android apps for all build types - usually Debug, Enterprise and Prod
  - Make sure to set the package name correctly and add SHA certificate hashes

### App distribution

- [ ] Select enterprise project and click on `Get started` button.

- [ ] Add Tester groups `Futured QA` and `Devs`. Create invite link and send it to the Slack channel.

- There are two ways of authorizing upload to Firebase.
  - Service account json, which is a **preferred way**
    1. Create service account in Google Cloud Console
    2. Select Firebase Products -> Firebase App Distribution Admin role (for this you need additional permissions, so either request them or let someone create the service account)
    3. Create new key with type `JSON`
    4. Create new repository secret `APP_DISTRIBUTION_SERVICE_ACCOUNT` with the json content
  - Using `FIREBASE_TOKEN`, which can be generated using Firebase CLI

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
