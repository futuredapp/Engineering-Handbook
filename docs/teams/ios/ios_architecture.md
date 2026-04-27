# iOS Architecture

This page describes the architecture used in our [futuredapp/FuturedKit](https://github.com/futuredapp/FuturedKit) repository.

The architecture is organized into layers, each with a specific responsibility. This ensures improved code organization, testability, and scalability while minimizing component coupling. We follow modern Swift and SwiftUI best practices.

![Architecture Light](Resources/ios_architecture_light.png#only-light){data-gallery="light"}
![Architecture Dark](Resources/ios_architecture_dark.png#only-dark){data-gallery="dark"}

## Overall Architecture

### User Interface

The **User Interface** is built with **SwiftUI** using **Components** and **ComponentModels**. A Component and its corresponding ComponentModel form a single "scene".

- each `Component<Model>` is a `struct` conforming to `View`, parameterized with a `Model` retained via `@State`
- components bind to a `ComponentModelProtocol` to allow mock implementations for seamless scene testing (e.g., `#Preview` feedback)
- child components receive their model as a plain property (not wrapped)
- user interactions are forwarded to the `ComponentModel` as simple function calls (e.g., `model.onButtonTapped()`)

### Presentation Layer

At the **presentation layer**, we employ an extended MVVM-C style approach with **Flow Coordinators** and optional **Flow Providers**.

- **Flow Coordinators** conform to `Coordinator` and manage creation, lifetime, navigation stacks, tabs, and modal presentations
  - `TabViewFlow` for tabbed flows
  - `NavigationStackFlow` for push/pop flows
- coordinators are `@Observable` classes with explicit `@MainActor` conformance
- coordinators control their flow using events emitted by `ComponentModels`
- **Flow Providers** encapsulate reusable sub-flows shared across multiple coordinators

### Data & Domain Layer

This layer handles shared state, business logic, and dependencies.

#### DataCache

The **`DataCache`** is an `@Observable @MainActor` class that serves as the **Single Source of Truth** for shared application data.

- reads and writes are synchronous from any `@MainActor` context (access via `dataCache.value`)
- `ComponentModels` use computed properties to derive data from the `DataCache`; observation tracking is automatic
- `Services` use `async/await` for asynchronous operations (networking, persistence) and update the `DataCache` with results
- can be used globally (one per app) or privately within a coordinator to provide flow-specific data

#### Services

Business logic, such as networking or data processing, is encapsulated in **`Services`**.

- responsible for performing external operations (e.g., fetching data from an API)
- used by `ComponentModels` to trigger actions and update the `DataCache` if needed

#### Container

The **`Container`** acts as a simple dependency injection hub. It is created at the app's root and passed down to each `Coordinator`. It is responsible for instantiating and providing shared dependencies such as the `DataCache` and other `Services`. Side effects that react to cache changes are co-located in the Container's `listeningTask`.

### Futured Macros

We use [futuredapp/futured-macros](https://github.com/futuredapp/futured-macros) to reduce boilerplate:

- **`@EnumIdentable`** — auto-conforms enums to `Identifiable` (used on coordinator `Destination` enums, marked `nonisolated`)
- **`@ProxyMembers`** — generates dynamic member lookup forwarding to reduce boilerplate
