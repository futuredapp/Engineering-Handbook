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
