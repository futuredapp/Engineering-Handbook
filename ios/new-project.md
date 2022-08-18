# New project checklist
***Note:** For more information check our [Project tools wiki](https://www.notion.so/futured/Project-tools-wiki-451531fedb8e4e1093e5e45bc4704227)*

## 1. Create new repository

- [ ] Open [iOS project template](https://github.com/futuredapp/iOS-project-template) and use it to create new repository. Preferred repo name is *[productname]-[platform]*, where *[productname]* includes only name of the product, without client name (like Skoda).
- [ ] Create `develop` branch and set is as a default branch.
- [ ] Go to Settings tab > General and disable **Wiki**, **Issues**, **Projects** features we do not use.
- [ ] Enable **Automatically delete head branches** so there is no need to delete branches manually after each PR merge.
- [ ] Add `futuredapp/ios` team in Access > Collaborators and teams.

- [ ] In Code and Automation > Branches, add branch protection rules to patterns `develop` and `main` branches exactly as shown in the following screenshot:

![GitHub branch protection](../general/attachments/GitHub_branch_protection.png)

***Note:** Status checks may not be available yet. You will set them later.

![GitHub branch protection1](../general/attachments/GitHub_branch_protection1.png)

![GitHub branch protection2](../general/attachments/GitHub_branch_protection2.png)

- [ ] Create new branch `feature/PROJ-1-setup-project` where everything will be set up in next steps.


## 2. Create a new Xcode project

- [ ] Write product name in CamelCased style. Organization Identifier is *app.futured*. Leave *Use Core Data* unchecked (persistence is usually implemented in much later phase of development) and *Include Tests* checked. (CI requires tests run)
- [ ] Change bundle identifier to kebab-cased style `app.futured.kebab-case-app-name`.

## 3. Configure Ruby environment and Fastlane

Update your Ruby using your preferred ruby version manager. and install latest version of dependency manager `gem install bundler`.

- [ ] Call `bundle install` to install gems.
- [ ] Edit `fastlane/Fastfile` to specify proper environment variables according to [imported Fastlane README](https://github.com/futuredapp/fastlane).
- [ ] Call `bundle exec fastlane create_apps`. This requires operations rights and you'll need to pass two factor authorization.

## 4. Add dependencies

Add following dependecies using Swift Package manager:

  - <https://github.com/futuredapp/FuturedKit> (SwiftUI project)
  - <https://github.com/futuredapp/FTAPIKit> (Project using REST API)
  - <https://github.com/futuredapp/FTTestingKit> (only to test target)

## 5. Configure SwiftLint

- [ ] Add a new Run Script Phase that executes the script (remove `swiftlint autocorrect` if you want to fix linting issues manually):

```
if which swiftlint >/dev/null; then
    swiftlint autocorrect
    swiftlint
else
    echo "error: SwiftLint not installed, run: brew install swiftlint"
fi
```

## 6. Configure the project for Continuous Deployment

- [ ] In Target's Signing & Capabilities tab, uncheck the *Automatically manage signing* checkbox.
- [ ] In Manage Schemes..., set root project scheme as Shared.
- [ ] Run `bundle exec fastlane test` to check whether test can be run on both CI and locally.
- [ ] In Xcode, go to Project Info screen. In Configurations section, add a new configuration by duplicating the **Release** configuration and rename it to **Enterprise**.
- [ ] On Build Settings tab for target (not project), look for Product Bundle Identifier, expand it to see preferences for Debug, Enterprise and Release configurations and for the Enterprise one, add suffix `.beta`.
- [ ] Select appropriate certificates, provisioning profiles for all configurations in the Target's Signing & Capabilities tab.
- [ ] Run `bundle exec fastlane enterprise` to check whether enteprise builds to App Center succeed.

## 7. Setup App Center

***Note:** The app should be automatically created and submitted to App Center to your account during step 6 by running `bundle exec fastlane enterprise` in Terminal.*

- [ ] Find the app under your account, go to its Settings, in top right corner find three dots and *Transfer app to organization* (select Futured).
- [ ] Add all internal teams as collaborators.
- [ ] Create distribution group for the client and invite them.

## 8. Configure continuous integration

Choose your CI service, we currently prefer Github Actions.

### GitHub Actions

Most of the configuration is already part of the [iOS project template](https://github.com/futuredapp/iOS-project-template). All the secrets are provided by the organization. If they are not available ask someone with admin permissions to provide them to your repository.

## 9. Open pull request with the project setup

- [ ] Update CODEOWNERS file.
- [ ] Update project README and fill in all the strike-through points.
- [ ] Commit everything and open PR and add this filled checklist to description.
- [ ] After CI checks the PR, status checks are now available to be added in Branch protections settings. Add them.

## 10. Enjoy!
