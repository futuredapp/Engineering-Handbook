# New project checklist

## 1. Create new repository

- [ ] Open [iOS project template](https://github.com/futuredapp/iOS-project-template) and use it to create new repository. Preferred repo name is *[productname]-[platform]*, where *[productname]* includes only name of the product, without client name (like Skoda).
- [ ] Create `develop` branch and set is as a default branch.
- [ ] Disable **Wiki**, **Issues**, **Projects** features we do not use.
- [ ] Enable **Automatically delete head branches** so there is no need to delete branches manually after each PR merge. (repo - general settings)

- [ ] Set branch protection rules to `develop` and `main` branches exactly as shown in the following screenshot:

![GitHub branch protection](../general/attachments/GitHub_branch_protection.png)

- [ ] Create new branch `feature/PROJ-1-setup-project` where everything will be set up in next steps.


## 2. Create a new Xcode project

- [ ] Start with the "Single View App" template. Make sure Organization Name is *Futured apps s.r.o.* and Organization Identifier is *app.futured*. Leave *Use Core Data* unchecked (persistence is usually implemented in much later phase of development) and *Include Unit Tests* and *Include UI Tests* checked. (CI requires tests run)

## 3. Configure Ruby environment and Fastlane

Update your system Ruby, preferably using `brew install ruby` and install latest version of dependency manager `gem install bundler`.

- [ ] Call `bundle install` to install gems.
- [ ] Edit `fastlane/Fastfile` to specify proper environment variables according to [imported Fastlane README](https://github.com/futuredapp/fastlane).
- [ ] Call `bundle exec fastlane create_apps`. You will need operations rights for this. If you are not in operations group, ask someone to do this for you.

## 4. Add dependencies

If Swift Package manager will be used for dependecy management:

- [ ] Remove `Podfile`.
- [ ] Open Xcode and add all relevant packages to the project

If CocoaPods will be used for dependecy management:

- [ ] Edit `Podfile` and remove dependencies which are not relevant to the project.
- [ ] Install fresh dependencies using `bundle exec pod update`.

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

- [ ] In Target's General tab, keep the Automatically manage signing checkbox unchecked.
- [ ] In Manage Schemes, set root project scheme as Shared.
- [ ] Run `bundle exec fastlane test` to check whether test can be run on both CI and locally.
- [ ] In Xcode, go to Project Info screen. In Configurations section, add a new configuration by duplicating the **Release** configuration and rename it to **Beta**.
- [ ] On Build Settings tab, look for Product Bundle Identifier, expand it to see preferences for Debug, Beta and Release configurations and for the Beta one, add suffix `.beta`.
- [ ] Select appropriate certificates, provisioning profiles for all configurations in the Signing section.
- [ ] Run `bundle exec fastlane beta` to check whether beta builds to TestFlight succeed.

## 7. Configure the project for automatic versioning

- [ ] Set proper values in Versioning part of Build Settings:
  - [ ] Change Marketing Version to `1.0.0`.
  - [ ] Change Current Project Version to `1`.

## 8. Configure continuous integration

Choose your CI service, we currently prefer Github Actions.

### GitHub Actions

Most of the configuration is already part of the [iOS project template](https://github.com/futuredapp/iOS-project-template). All the secrets are provided by the organization. If they are not available ask someone with admin permissions to provide them to your repository.

## 9. Open pull request with the project setup

- [ ] Update CODEOWNERS file.
- [ ] Update project README and fill in all the strike-through points.
- [ ] Commit everything and open PR and add this filled checklist to description.

## 11. Enjoy!
