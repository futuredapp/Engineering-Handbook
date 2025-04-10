# Android-native architecture

This page describes architecture used in our [futuredapp/android-project-template-compose](https://github.com/futuredapp/android-project-template-compose) repository.

The architecture is organized into layers, each with a specific responsibility. This ensures:

- Improved code organization and maintainability
- Easier testing of individual layers
- Better scalability and adaptability
- Minimized component coupling

We use [Android Jetpack](https://developer.android.com/jetpack) libraries to simplify development and follow modern Android best practices. These libraries help create reliable and maintainable apps.

![](../Resources/architecture/arch_native_light.png#only-light)
![](../Resources/architecture/arch_native_dark.png#only-dark)

## Overall Architecture

### User Interface

**User Interface** is written in [Jetpack Compose](https://developer.android.com/compose),
which is a declarative framework for creating UI and is also the official,
Google-recommended way of implementing UI.

### Presentation Layer

At the **presentation layer**, we follow the MVVM design pattern, where each screen has its own ViewModel that
holds the screen state and contains presentation logic.
We adhere to the Unidirectional Data Flow pattern, where user actions from the UI are processed by the ViewModel,
which then mutates the screen state. This screen state is then reflected back to the UI.
The app uses standard [Lifecycle](https://developer.android.com/jetpack/androidx/releases/lifecycle) components from androidx set of libraries.

### Domain Layer

The **domain layer** implements UseCases / facades, handles the application's business logic,
and communicates with other layers using Kotlin Coroutines. We developed [futuredapp/arkitekt](https://github.com/futuredapp/arkitekt) to help with this.

UseCases provide a clean API for the presentation layer by interacting with repositories and services.
Each UseCase is tied to the ViewModel's lifecycle Coroutine scope to ensure proper cancellation.

### Data Layer

The final - **data layer** holds the global application state, implements caching,
and provides the domain layer with access to various data sources (e.g., database, API service, etc.).
The entire data layer is observable (this enables automatic UI updates when data changes)
and should act as a Single Source of Truth to other layers.

All components in the application are injectable using a Dependency Injection (DI) framework. We use [Dagger Hilt](https://dagger.dev/hilt/) by default.
The DI also allows individual components to be tested separately using Unit tests.
