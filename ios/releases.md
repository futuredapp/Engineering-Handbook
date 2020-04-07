# Release checklist

## What to check before every release

- [ ] **Update from previous version** (data migration, no unnecessary logouts).
- [ ] Demo account (will Apple review be able to access most parts of the app if it requires login?)
- [ ] Release notes (every version should have different release notes)
- [ ] Visually check screenshots if they are up-to-date

## Source control steps to release

1. Create a branch `release/[version]`, where `[version]` is the new build number to be released.
2. Bump the build number and version number. Build numbers are incremental, starting with `1`. Version numbers have `x.y.z` format, where `x` represents major, `y` minor and `z` patch number.

     Fastlane must be used for this by calling `bundle exec fastlane bump`, `bundle exec fastlane bump type:minor`, `bundle exec fastlane bump type:major`.
3. Commit and push changes. Send a pull request from `release/[version]` to `master`.
4. When merged, create a new tag on `master` with `[version]` value. 
5. Create a pull request from `master` to `develop`.
6. After merge tag latest commit in `master` with semantic version `x.y.z`.
7. If Bitrise deployment is configured, release build and App Store submission will be processed automatically. Otherwise call `bundle exec fastlane beta` to make the build manually.
8. When build is processed to iTunes Connect, submit it to TestFlight first and let the tester go through it and ideally go through it yourself too.
9. Only when tester approves the build, proceed with sending the update to the review.
