# Release process overview

!!! warning "TODO: Please feel free to complete or modify this section as needed."

1. Review [Git flow](https://www.gitkraken.com/learn/git/git-flow) for guidelines on working with branches.
2. Examine the release flowchart:
    ![](../Resources/release/google_play_release_flow.png)
3. Create a Pull Request from the `develop` branch to the `main` branch.
    - Use a PR name in the format: `Release/X.X.X`, where `X.X.X` represents the version number.

4. Wait for the PR to be approved and for the "Check PR" workflow to complete successfully.
5. Create a new release tag in GitHub.
    - Use a tag name in the format: `X.X.X`, where `X.X.X` represents the version number.
    - Populate or generate the release notes.
6. Wait for CI to build the release.
7. Verify the release build(s) in the Firebase Console, Google Play Console, or other relevant platforms.
8. If all checks pass, publish the release to the designated test channels (e.g., Internal, Closed, or Open testing, depending on the project).
9. Notify the client about the new release 🎉 available in the test channels.
10. Wait for the client to test the release and provide feedback.
11. If the client approves the release, proceed to publish it to production 🚀🚀🚀
