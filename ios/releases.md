# Release checklist

## What to check before every release

- [ ] **Update from previous version** (data migration, no unnecessary logouts).
- [ ] Demo account (will Apple review be able to access most parts of the app if it requires login?)
- [ ] Release notes (every version should have different release notes)
- [ ] Visually check screenshots if they are up-to-date

## Source control steps to release

1. Create a branch `release/[version]`, where `[version]` is the new build number to be released.
2. Commit and push changes. Send a pull request from `release/[version]` to `main`.
3. When merged, create a new tag on `main` with `[version]` value. 
4. Create a pull request from `main` to `develop`.
5. After merge, create new release on GitHub from `main` branch with semantic version tag `x.y.z`. If continuous delivery is configured, release build and App Store submission will be processed automatically.
6. When build is processed to App Store Connect, let the tester go through it and ideally go through it yourself too.
7. Only when tester approves the build, proceed with sending the update to the review.
