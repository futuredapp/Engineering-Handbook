# Release checklist

## What to check before every release

- [ ] **Update from previous version** (data migration, no unnecessary logouts).
- [ ] Demo account (will Apple review be able to access most parts of the app if it requires login?)
- [ ] Release notes (every version should have different release notes)
- [ ] Visually check screenshots if they are up-to-date

## Source control steps to release

1. Create a branch `release/[version]-[build_number]`, where `[version]` is the new build number to be released
   and `[build_number]` will be the incremented build number.
2. Bump the build number and version number. Build numbers are incremental, starting with `1`. Version numbers have `x.y.z` format,
   where `x` represents major, `y` minor and `z` patch number.

   Fastlane must be used for this by calling `bundle exec fastlane bump`, `bundle exec fastlane bump type:major`, `bundle exec fastlane bump type:minor`, `bundle exec fastlane bump type:patch`.
3. Commit and push changes. Send a pull request from `release/[version]` to `main`.
4. When merged, create a new tag on `main` with `[version]` value. 
5. Create a pull request from `main` to `develop`.
6. After merge, create new release on GitHub from `main` branch with semantic version tag `x.y.z-[build_number]`.
7. If continuous delivery is configured, release build and App Store submission will be processed automatically. Otherwise call `bundle exec fastlane beta` to submit the build manually.
8. When build is processed to App Store Connect, submit it to TestFlight first and let the tester go through it and ideally go through it yourself too.
9. Only when tester approves the build, proceed with sending the update to the review.
