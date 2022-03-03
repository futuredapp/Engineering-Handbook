# Project Structure & Code Style

## Project structure

```
Project
|
|– AppCoordinator.swift
|– AppDelegate.swift
|– Configuration.swift
|– Models
|–– User.swift
|– Services
|–– HomeService.swift
|– Scenes
|–– HomeScene
|––– HomeScene.storyboard
|––– HomeSceneCoordinator.swift
|––– HomeSceneViewController.swift
|––– HomeSceneViewModel.swift
|– Resources

```

### Templates

For the simple and consistent implementation of scenes, we use Xcode templates for the modal scene, push scene, root scene, and simple service. Scene templates consist of:

- Storyboard and storyboard definition
- View controller
- View model
- Coordinator

The templates are open-source and [available on GitHub](https://github.com/thefuntasty/MVVM-C-Template).

## Code Style

The preferred code style is guarded by [SwiftLint](https://github.com/realm/SwiftLint) and defined by rules contained in the `.swiftlint.yml` configuration file. Small deviations and certain syntax forms are automatically corrected by [SwiftFormat](https://github.com/nicklockwood/SwiftFormat), configured by the `.swiftformat` file. Both configuration files utilized within Futured apps can be found in [iOS project template](https://github.com/futuredapp/iOS-project-template).

### SwiftLint

SwiftLint is usually set up as a regular build step and utilizes configuration found in `.swiftlint.yml`. Default active rules are supplemented by opt-in rules, which can be found categorized in the configuration file.

### SwiftFormat

SwiftFormat should be used more carefully, as it automatically adds changes in the code. It is recommended to trigger the SwiftFormat after committing the added changes, hence it is most practical as a part of a separate build scheme. SwiftFormat uses its own `.swiftformat` configuration, which is usually global for the whole project but can be added also added to separate project sub-folders. 

By default, all rules are applied which can be problematic for an existing project. There is a very useful `--inferoptions` execution flag, which makes no changes but proposes an ideal rule configuration for a given code style. Another useful execution flag is `--verbose` which prints out every file found, whether there were changes, and what rules were applied. It helps with initial runs, when the developer is not already familiar with rule names, however, the run takes significantly longer, so it should be only used when needed.