## Code style & Project Structure

Code style is mostly defined by [SwiftLint rules](.swiftlint.yml).

### Project structure

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

For simple and consistent implementation of scenes we use Xcode templates for modal scene, push scene, root scene and simple service. Scene templates consist of:

- Storyboard and storyboard definition
- View controller
- View model
- Coordinator

The templates are open-source and [available on GitHub](https://github.com/thefuntasty/MVVM-C-Template).
