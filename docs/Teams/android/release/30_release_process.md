# Release process overview

!!! warning "TODO: Nevím, co vše tady chceme mít. Feel free doplnit/upravit"

1. Check [Git flow](https://www.gitkraken.com/learn/git/git-flow) how to work with branches.
2. Check release flowchart
    ![](../Resources/release/google_play_release_flow.png)
3. Create Pull request from `develop` to `main` branch.
    - Use PR name like: `Release/X.X.X` where `X.X.X` is version.

4. Wait for approval and also for the Check PR workflow to complete.
5. Create a new release tag in GitHub.
    - Use tag name like `X.X.X` where `X.X.X` is version.
    - Fill or generate the release notes.
6. Wait for CI to build the release.
7. Check the release(s) in the Firebase / Google Play Console etc.
8. If everything is OK, publish the release to test channels (Internal / Closed / Open testing - depends on the project).
9. Inform the client about the new release 🎉 in the test channels.
10. Wait for the client to test the release and provide feedback.
11. If the client approves the release, publish it to production ,🚀,🚀,🚀
