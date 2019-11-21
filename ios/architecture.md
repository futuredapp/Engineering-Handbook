# Architecture

In Futured, we enforce our customized version of architecture described as MVVM-C (Model-View-ViewModel Coordinator). Only occasionally a different architecture is used, if product requires it or as a way to experiment and explore.

## MVVM-C

The basic MVC architecture introduced by Apple is in some cases really cumbersome and the view controller often becomes omnipotent and has too much responsibilities. Therefore this architecture introduces another layers called view model and coordinator.

The view model is responsible for each screen's logic. The view controller should not handle any logic and only setup the interface and present the data received from view model. Also the view controller sends all actions to view model so it can handle them. The view model is the only layer, that handles state changes and the only place where the models are held. The models are retrieved from services which are injected using simple dependency injection container called service holder. 

Services are mainly used for encapsulating some low-level frameworks like communication with server or persistence. They create higher-level API and enable simper testing by mocking them.

The last layer in the MVVM-C architecture are coordinators (routers or navigators if you prefer). Coordinator is responsible for setting up the scene, navigating to it, back from it and navigating to other screens. In FuntastyKit there are some basic implementations of basic navigation patterns, namely push and modal. Of course, the concept is not limited to these basic patterns and is highly configurable.

![MVVM-C architecture diagram](attachments/Figure.001.png)

## Dependency injection

As a simple method for dependency injection a container called *service holder* is used. At the start of the application, first coordinator called `AppCoordinator` sets up one container and instantiates/adds the services to it, which are then available as singletons or multitons. Services can handle interaction with web APIs, persistence and pack other system APIs. Each service is primarily a protocol, so all the services are easily testable and mockable.

When the coordinator is navigating to other scenes, it passes the service holder along to them. The newly created coordinator then injects all the services the view model needs for handling its logic using the constructor injection.

## Supporting frameworks

### FuntastyKit

[FuntastyKit](https://github.com/thefuntasty/FuntastyKit) is an open-source Swift framework containing the architecture-related code and some regularly used UIKit extensions. 

The key parts: 

- *architecture/Coordinator.swift* – set of protocols and protocol extensions to back up the functionality of coordinators
- *architecture/ServiceHolder.swift* – a simple dependency injection container
- *autoLayout* – AutoLayout extensions
- *errors* – error structures and coordinator extensions unifying the error handling logic
- *extensions* – UI-related extensions
- *protocols* – Nibable for easy view initialization form xib, StoryboardType for type-safe references to storyboards

### Asynchronous programming

We use [PromiseKit](https://github.com/mxcl/PromiseKit) as a way to simplify asynchronous programming. Promises are powerful, easy to learn mechanisms, allowing us to get rid of competition handlers without bringing the complexity of a reactive framework.

Where appropriate, we're also open to more declarative approaches such as [RxSwift](https://github.com/ReactiveX/RxSwift).