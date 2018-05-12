# Release checklist

1. Create a branch `release/[version]`, where *[version]* is the new build number to be released.
2. Bump the bulid number and version number. Build numbers are incremental, starting with 1. Version numbers have *x.y(.z)* format, where *x* represents major, *y* minor and *z* patch number.
	
	Fastlane can be used for this by calling `fastlane bump_patch`, `fastlane bump_minor`, `fastlane bump_major`.
3. Commit and push changes. Send a pull request from *release/[version]* to *master*.
4. When merged, create a new tag on *master* with `[version]` value. 
5. Create a pull request from *master* to *develop*.
5. If Bitrise deployment is configured, release build and App Store submission will be processed automatically. Otherwise call `fastlane beta` to make the build manually.
6. When build is processed to iTunes Connect, submit it to TestFlight first and let the tester go through it.
7. Only when tester approves the build, proceed with sending the update to the review.