# Kotlin Multiplatform Architecture

This page describes the Kotlin Multiplatform architecture used in our [futuredapp/kmp-futured-template](https://github.com/futuredapp/kmp-futured-template) repository that targets Android and iOS platforms.

**Our architecture focuses on high code sharing between platforms while respecting platform-specific best practices for user experience.**

The architecture is organized into layers, each with a specific responsibility, and utilizes a shared codebase where possible. This ensures:

- Improved code organization and maintainability
- Easier testing of individual layers
- Better scalability and adaptability
- Minimized component coupling

![](../Resources/architecture/arch_kmp_light.png#only-light){data-gallery="light"}
![](../Resources/architecture/arch_kmp_dark.png#only-dark){data-gallery="dark"}

## Overall Architecture

The architecture follows a multi-layered approach with platform-specific UI implementations on top of a shared KMP codebase.

**User Interface** is platform-specific, using [Jetpack Compose](https://developer.android.com/compose) on Android 
and [SwiftUI](https://developer.apple.com/xcode/swiftui/) on iOS. 
This approach leverages each platform's native UI capabilities while maintaining a consistent user experience.

**Presentation Layer** follows an MVI-like pattern using [Decompose](https://github.com/arkivanov/Decompose) for sharing presentation logic and navigation state.
Components manage state, process events, and handle navigation across both platforms. The state is exposed to the UI using Kotlin StateFlow.

The **domain layer** implements UseCases / facades, handles the application's business logic,
and communicates with other layers using Kotlin Coroutines.

**Data Layer** holds the application state, provides caching, and gives the domain layer access to various data sources (network, local storage).
It acts as a Single Source of Truth for the application.

## Key Components

### Decompose Components

The core of the presentation layer is built around Decompose components:

- `BaseComponent`: Foundation for all components that handles state management, lifecycle, and UI events
- `AppComponent`: Base for application-level components that don't require navigation
- `ScreenComponent`: Base for screen components that implement navigation actions

Components follow a unidirectional data flow where:

1. UI triggers actions
2. Components process actions
3. State is updated and flows back to the UI
4. One-shot UI events are dispatched when needed

### Navigation System

Navigation state is fully shared in KMP and is implemented using Decompose's powerful [navigation models](https://arkivanov.github.io/Decompose/navigation/overview/) such as ChildStack and ChildSlot, which provide type-safe, state-based navigation.

- **NavHosts**: Navigation is structured into several navigation trees. Each root of the tree is a Component which holds and manages state for its children. We call them NavHosts.
- **Navigation Actions**: Screen Components expose navigation interfaces that decouple UI from navigation logic. NavHosts implement these interfaces to manage navigation state. 
- **Deep Linking**: Native deep link intents/URLs are processed through a resolver that maps them to the appropriate navigation state.

This state-based approach allows navigation to be fully reactive, serializable, and recoverable after process death or configuration changes. The navigation structure is shared between platforms, allowing for consistent user flows while respecting platform UI conventions.

### UseCases

The domain layer operates through UseCases that encapsulate business logic:

- `UseCase`: For single-value operations
- `FlowUseCase`: For streaming data updates using Kotlin Coroutines Flow

Use Cases provide a clean API for the presentation layer by interacting with repositories and services. Each UseCase is tied to the screen Component's lifecycle Coroutine scope to ensure proper cancellation.

Our KMP architecture forks implementation
from our [futuredapp/arkitekt](https://github.com/futuredapp/arkitekt).

### Dependency Injection

[Koin](https://insert-koin.io/) is used for dependency injection across the codebase with:

- Annotation-based approach for module definition using Koin annotations
- Assisted injection for Decompose Components, simplified with our KSP compiler
- Platform-specific module overrides where needed

### iOS Interop Layer

While the Android app interacts with shared code directly, the iOS uses:

- [SKIE](https://skie.touchlab.co/) for improved Kotlin-Swift interop
- Swift wrappers around Kotlin StateFlows
- Swift ViewModels that adapt Kotlin components to SwiftUI

### Shared Resources

We use [moko-resources](https://github.com/icerockdev/moko-resources) for sharing strings and other resources.
The `StringDesc` interface allows us to provide strings to the platform directly from KMP state in platform-agnostic way.
This allows us to format strings directly in KMP code which ensures that strings are displayed on both platforms the same.

## Platform Integration

### Android Integration

Android consumes KMP code as a standard library module - from Android perspective, the shared code is just another Gradle dependency.

### iOS Integration

iOS integration is accomplished through:

- Swift Package wrapping a static KMP XCFramework
- Swift property wrappers that adapt Kotlin StateFlows to SwiftUI StateObjects

### Platform Bindings

Sometimes we need to execute platform-specific code in shared code. We use platform bindings for this purpose.

The platform binding is a Kotlin interface that is implemented on all platforms and provided to KMP during initialization.

We use this pattern for accessing native functionality not available in Kotlin common code:

- Device information (OS version, device model)
- Platform services (crash reporting, analytics)
- Notification services
- etc.
