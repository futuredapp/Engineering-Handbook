# SwiftUI Multiplatform

SwiftUI lets you target iPhone, iPad, and Mac from a single codebase. The most important architectural decision is which **navigation container** to use at the root. This single choice determines how your app looks and behaves on every platform.

This guide compares the two primary approaches: `TabView` with `.sidebarAdaptable` and `NavigationSplitView`.

!!! note
    The `TabView` with `.sidebarAdaptable` style and the new `Tab` API require **iOS 18 / macOS 15** or later. Projects with a lower deployment target cannot use this approach and would need a custom sidebar implementation instead.

## Platform Specifics

| Platform | Input | Layout | Key considerations |
|---|---|---|---|
| **iPhone** | Touch, gestures | Single column, portrait-first | • Design here first — it's the most constrained target<br>• Everything else is an additive layer |
| **iPad** | Touch + optional keyboard/trackpad | Multi-column, size class adaptive | • Handle both compact (slide-over) and regular width<br>• Support keyboard, trackpad, drag-and-drop, multi-window |
| **Mac** | Mouse + keyboard | Persistent multi-column, menu bar | • Users expect keyboard shortcuts and right-click menus<br>• Avoid iOS-isms like bottom bars and floating sheets |
| **watchOS** | Digital Crown, tap | Minimal list/detail | • Only a SwiftUI subset is available<br>• Best as a companion (glances, complications), not a standalone app |
| **tvOS** | Siri Remote (swipe to move focus) | Full-screen, focus-based | • No touch — navigation moves a focus highlight between elements<br>• Suited for media browsing |

## Xcode Setup

The recommended approach is a **single multiplatform target** rather than separate targets per platform:

- **One codebase, one target** — Xcode manages platform-specific builds while sharing logic. Each platform still produces its own binary, but they live under the same project and scheme.
- **Shared app identity** — a single Bundle ID means one App Store listing and one set of user reviews across platforms.
- **Universal Purchase** — customers buy once and get the app on all platforms (iOS, iPadOS, macOS). Separate targets require separate purchases.
- **Simpler CI/CD** — one scheme to build, test, and archive.

### Creating a multiplatform target

Use the **Multiplatform** app template when creating a new project. For existing projects, add platforms under the target's **Supported Destinations**. Set deployment targets per platform — typically the last two major versions.

<img src="../Resources/ios_multiplatform_xcode_new_project.png" style="max-width: 70%;">

### FuturedKit

The core of FuturedKit (`ComponentModel`, `Coordinator`, `DataCache`) is already platform-agnostic. `NavigationStackFlow` and `TabViewFlow` already have `#if os(macOS)` guards in place.

What is not yet supported on macOS: the `SceneDelegate` (uses UIKit scene lifecycle), all image picker helpers (UIKit-only), and the `TextStyle`/`Font` extension system (built on `UIFont` and `UIFontMetrics`). These would need macOS-specific implementations or a cross-platform abstraction layer before FuturedKit can be declared as a multiplatform dependency.

!!! warning
    Account for this migration when scoping a multiplatform project. Until FuturedKit fully supports macOS, the affected components need to be reimplemented or replaced per-target.

## Platform-Specific Code

SwiftUI handles most platform differences automatically, but sometimes you need to diverge. Two tools cover the majority of cases.

### Compile-time

Use `#if os(iOS)` / `#if os(macOS)` to include or exclude code at compile time. This is the right choice when an API simply does not exist on another platform.

```swift
#if os(iOS)
Tab(role: .search) { ... }
#endif
```

Keep `#if` blocks small and push them to the edges of your view hierarchy. Wrapping an entire screen in `#if` is a sign the screen should be split into platform-specific files instead.

### Size classes

Use `@Environment(\.horizontalSizeClass)` to adapt layout at runtime based on available width. This is more flexible than OS checks because the same code path runs on all platforms — it just renders differently.

```swift
@Environment(\.horizontalSizeClass) private var sizeClass

var body: some View {
    if sizeClass == .compact {
        compactLayout
    } else {
        regularLayout
    }
}
```

A compact size class means iPhone portrait, iPhone landscape (on smaller models), or iPad in slide-over. Regular means iPad full-screen and Mac.

## Navigation Paradigms

### TabView with .sidebarAdaptable

`TabView` with the `.tabViewStyle(.sidebarAdaptable)` modifier gives you a tab bar on iPhone and a sidebar on iPad/Mac, all from a single declaration.

- **iPhone** — standard bottom tab bar. Each tab owns its own `NavigationStack`.
- **iPad** — sidebar in landscape, pill bar in portrait. Sections and shortcuts via `TabSection`.
- **Mac** — fixed sidebar, keyboard shortcuts with `⌘1/2/3` via `Commands`.

<div style="display: flex; gap: 1rem; align-items: flex-end; margin-bottom: 1rem; width: 90%;">
  <img src="../Resources/ios_multiplatform_tabs_iphone.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
  <img src="../Resources/ios_multiplatform_tabs_ipad.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
</div>
<img src="../Resources/ios_multiplatform_tabs_mac.png" style="max-width: 70%;">

#### iPad: Sidebar and Pill Bar

On iPad, you can toggle between a collapsible sidebar and a compact pill bar. The pill bar trades navigation hierarchy for maximum content space.

<img src="../Resources/ios_multiplatform_tabs_ipad_sidebar.png" style="max-width: 70%; display: block; margin-bottom: 1rem;">
<img src="../Resources/ios_multiplatform_tabs_ipad_pill.png" style="max-width: 70%; display: block;">

### NavigationSplitView

`NavigationSplitView` provides a two- or three-column layout that collapses gracefully on smaller screens.

- **iPhone** — columns collapse into standard push navigation. Visually identical to a `NavigationStack` app.
- **iPad** — all columns visible simultaneously (sidebar, list, detail).
- **Mac** — persistent three-column layout. Feels like a native Mac app (Mail, Finder).

<div style="display: flex; gap: 1rem; align-items: flex-end; margin-bottom: 1rem; width: 90%;">
  <img src="../Resources/ios_multiplatform_split_iphone.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
  <img src="../Resources/ios_multiplatform_split_ipad.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
</div>
<img src="../Resources/ios_multiplatform_split_mac.png" style="max-width: 70%;">

## Platform Comparison

|  | TabView (.sidebarAdaptable) | NavigationSplitView |
|---|---|---|
| **iPhone navigation UI** | Bottom tab bar | No tab bar — sidebar is root, columns push full-screen |
| **iPad navigation UI** | Sidebar (landscape) / pill bar (portrait) | Three columns always visible |
| **Mac navigation UI** | Fixed sidebar | Fixed sidebar + list + detail columns |
| **Detail presentation** | Sheet (floats over content) | Inline column (list stays visible) |
| **Sidebar behavior** | Collapsible, same content in both | Collapsible, same content in both |
| **Search** | Dedicated Search tab on iOS | Search bar inside the content list |
| **Mac feel** | Sidebar-driven, familiar but not fully native | Native list-detail app (Mail, Finder pattern) |

## Detail Presentation: Sheet vs Inline

The biggest UX difference between the two approaches is how detail content is presented on iPad and Mac.

??? note "TabView — sheet presentation"

    Detail opens as a floating sheet that covers the list. You must dismiss the sheet to return to the list.

    <img src="../Resources/ios_multiplatform_tabs_ipad_detail.png" style="max-width: 70%; display: block; margin-bottom: 1rem;">
    <img src="../Resources/ios_multiplatform_tabs_mac_detail.png" style="max-width: 70%; display: block;">

??? note "NavigationSplitView — inline column"

    Detail appears in a dedicated third column. The list remains visible and interactive, making it easy to scan or compare items.

    <img src="../Resources/ios_multiplatform_split_ipad_detail.png" style="max-width: 70%; display: block; margin-bottom: 1rem;">
    <img src="../Resources/ios_multiplatform_split_mac_detail.png" style="max-width: 70%; display: block;">

## Search

The `.searchable` modifier adapts its placement based on the navigation container.

### TabView — dedicated Search tab

On iOS and iPadOS, `TabView` supports `Tab(role: .search)` which creates a dedicated Search tab in the tab bar. On iPad, it moves to the top of the sidebar or pill bar. On Mac, the search field appears in the toolbar of the selected sidebar item. `Tab(role: .search)` is not available on macOS, so wrap it in an `#if os(iOS)` guard.

<div style="display: flex; gap: 1rem; align-items: flex-end; margin-bottom: 1rem; width: 90%;">
  <img src="../Resources/ios_multiplatform_tabs_iphone_search.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
  <img src="../Resources/ios_multiplatform_tabs_ipad_search.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
</div>
<img src="../Resources/ios_multiplatform_tabs_mac_search.png" style="max-width: 70%;">

### NavigationSplitView — inline search bar

`.searchable` places the search field directly inside the content list on all platforms. No dedicated tab, no platform-specific code.

<div style="display: flex; gap: 1rem; align-items: flex-end; overflow-x: auto;">
  <img src="../Resources/ios_multiplatform_split_iphone_search.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
  <img src="../Resources/ios_multiplatform_split_mac_search.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
</div>

## Adaptive Table

SwiftUI `Table` automatically adapts across platforms:

- **iPad / Mac** — renders all columns (multi-column table).
- **iPhone** — shows only the first column, effectively becoming a list.

This is a free multiplatform win that requires no `#if` checks or platform-specific code.

<div style="display: flex; gap: 1rem; align-items: flex-end; margin-bottom: 1rem; width: 90%;">
  <img src="../Resources/ios_multiplatform_table_iphone.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
  <img src="../Resources/ios_multiplatform_table_ipad.png" style="height: 340px; width: auto; flex-shrink: 0; max-width: none;">
</div>
<img src="../Resources/ios_multiplatform_table_mac.png" style="max-width: 70%;">

## Liquid Glass

Liquid Glass is Apple's cross-platform design language introduced in iOS 26 / macOS 26. It applies a unified translucent, reflective material to system UI elements — tab bars, sidebars, navigation bars, toolbars — across iPhone, iPad, and Mac.

If you use standard SwiftUI navigation containers, you get Liquid Glass automatically. No code changes required. This is a meaningful shift for multiplatform: the visual gap between an iOS app and its Mac counterpart is now significantly smaller out of the box.

!!! note
    Liquid Glass requires iOS 26 / macOS 26 or later. Custom tab bars, sidebars, or other UI containers you build yourself do not adopt it automatically — apply `.glassEffect()` explicitly where needed.

## When to Use Which

**Use `TabView` (.sidebarAdaptable) when:**

- The app is **section-oriented** — major features each need a tab bar presence on iPhone.
- Detail content is supplementary and a **modal sheet** is acceptable.
- You want sidebar filter shortcuts (`TabSection`) on iPad/Mac.
- You need the iOS dedicated Search tab (`Tab(role: .search)`).

**Use `NavigationSplitView` when:**

- The core interaction is **selecting an item and viewing its detail**.
- **iPad and Mac are a priority** — persistent side-by-side detail matters.
- You want the simplest navigation state (two `@State` properties, no derived bindings).
- The app should **feel native on Mac** — list-detail is a Mac convention.

**Combine both:**

Use `TabView` at the root with `NavigationSplitView` inside one or more tabs. This is what Mail, Notes, and Shortcuts do: tabs for top-level sections, split view for list-detail inside each.

## Sources

- [Configuring a multiplatform app target](https://developer.apple.com/documentation/xcode/configuring-a-multiplatform-app-target)
- [Food Truck: Building a SwiftUI multiplatform app](https://developer.apple.com/documentation/swiftui/food-truck-building-a-swiftui-multiplatform-app)
- [Adopting Liquid Glass](https://developer.apple.com/documentation/TechnologyOverviews/adopting-liquid-glass)

### Human Interface Guidelines

- [Designing for iOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ios)
- [Designing for iPadOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados)
- [Designing for macOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-macos)
- [Tab bars](https://developer.apple.com/design/human-interface-guidelines/tab-bars)
- [Sidebars](https://developer.apple.com/design/human-interface-guidelines/sidebars)
- [Split views](https://developer.apple.com/design/human-interface-guidelines/split-views)
