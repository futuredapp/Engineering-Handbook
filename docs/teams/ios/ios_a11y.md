# Accessibility + European Accessibility Act 2025 (EAA)

!!! info "Last update: October 2025"

!!! info "Disclaimer"

    The purpose of this document is only to provide a simple cheatsheet of what you, as a developer, have to bear in mind. To be fully EAA compliant, the guide must also address the mandatory accessibility requirements for multimedia content, user support processes and documentation, and specific app functionalities like adjustable time limits. Also, to fully comply with the EAA, you must provide fully accessible customer support, documentation, and a public Accessibility Statement, not just an accessible app.

## Key Deadlines and Enforcement

!!! warning "Compliance Deadline: June 28, 2025"

- All new products and services launched within the EU market after this date must comply with the EAA requirements. Existing services have a transition period and must be compliant by **June 28, 2030**!
- The EAA is not a recommendation; it is a legal requirement. Enforcement will be handled by designated authorities within each EU member state.
- Who is affected? The act applies to any company, regardless of its location, that sells covered products or services to consumers within the European Union.
- Penalties for Non-Compliance: While specific penalties are determined by each member state, they are designed to be "effective, proportionate, and dissuasive." They can include:
    - Significant fines.
    - Withdrawal of the non-compliant product/service from the market.
    - Public notices about the non-compliance.

## What app categories are required for compliance

!!! quote

    The EAA specifically covers mobile apps in certain areas, while this doesn’t cover all apps, it’s safe to say these categories make up a significant section of mobile apps available. 

-   **E-commerce services:** Any app selling products or services.
-   **Banking services:** Mobile banking apps.
-   **Transport services:** Apps for buying tickets for air, bus, rail, and water transport, as well as real-time travel information.
-   **E-books:** Apps for reading and distributing e-books.
-   **Audiovisual media services:** Streaming services and on-demand platforms.
-   **Access to electronic communication services:** Apps for messaging, email, and calling.

## What are the basics to conform the EAA

The EAA's requirements are based on the globally recognized **Web Content Accessibility Guidelines (WCAG)**. Conformance is built on four core principles, often remembered by the acronym **POUR**. Your app must be:

### 1. Perceivable

- Users must be able to perceive the information being presented; it can't be invisible to all of their senses.
- Provide text alternatives: All non-text content (icons, images) must have a text alternative. Use .accessibilityLabel for this.
``` swift
Image("profile-icon")
    .accessibilityLabel("User Profile")
```
- Support text resizing: Your app must respect the user's font size settings. Use Dynamic Type with system text styles (e.g., .body, .headline) instead of fixed font sizes.
- Ensure sufficient contrast: Text and important UI elements must have a color contrast ratio of at least 4.5:1 against the background. For large text, the ratio is 3:1.
- Don't rely on color alone: Convey information using more than just color. Support the Differentiate Without Color setting. For example, use an icon in addition to a red color for an error state.

### 2. Operable

- Users must be able to operate the interface; the interface cannot require interaction that a user cannot perform.
- Support assistive technologies: All functionality must be operable through assistive technologies like VoiceOver, Switch Control, and Voice Control. Test your app thoroughly with VoiceOver.
- Provide large touch targets: Interactive elements should have a minimum touch target size of 44x44 points to be easily tappable.
- Respect motion settings: Your app must check for and respect the Reduce Motion setting. Disable non-essential animations when it's enabled.
- Avoid seizure triggers: Content must not flash more than three times in any one-second period.

### 3. Understandable

- Users must be able to understand the information as well as the operation of the user interface.
- Use clear language: Write in simple, clear language. Avoid jargon.
- Be consistent: Use consistent navigation, icons, and terminology throughout your app. A "Save" button should always look and behave the same way.
- Provide clear error messages: When an error occurs, explain what went wrong and how the user can fix it. Don't just say "Invalid input."

### 4. Robust

- Content must be robust enough that it can be interpreted reliably by a wide variety of user agents, including assistive technologies.
- Use standard components: Whenever possible, use standard SwiftUI and UIKit components. They have accessibility built-in.
- Define accessibility traits: For custom components, explicitly define their roles using accessibility traits like .isButton or .isHeader so assistive tech knows what they are.
- Manage focus: Ensure the VoiceOver focus order is logical and predictable, especially in complex views or after presenting a new screen. Use the .accessibilitySortPriority and .accessibilityElement(children: .contain) modifiers to manage focus.

## Recommended Views

### 1. Dynamic Horizontal Stack

will change from horizontal to vertical stack once dynamic font exceeds certain size

??? note "DynamicHStack"

    ``` swift 
    import SwiftUI

    struct DynamicHStack<Content: View>: View {
        struct VStackConfig {
            let spacing: CGFloat?
            let alignment: HorizontalAlignment

            init(spacing: CGFloat? = nil, alignment: HorizontalAlignment = .leading) {
                self.spacing = spacing
                self.alignment = alignment
            }
        }

        struct HStackConfig {
            let spacing: CGFloat?
            let alignment: VerticalAlignment

            init(spacing: CGFloat? = nil, alignment: VerticalAlignment = .center) {
                self.spacing = spacing
                self.alignment = alignment
            }
        }

        @Environment(\.sizeCategory) private var sizeCategory

        private let breakingSize: ContentSizeCategory
        private let verticalConfig: VStackConfig
        private let horizontalConfig: HStackConfig
        private let content: Content

        init(
            breakingSize: ContentSizeCategory = .extraExtraExtraLarge, // swiftlint:disable:this function_default_parameter_at_end
            hStackConfig: HStackConfig = .init(),
            vStackConfig: VStackConfig = .init(),
            @ViewBuilder content: () -> Content
        ) {
            self.breakingSize = breakingSize
            self.verticalConfig = vStackConfig
            self.horizontalConfig = hStackConfig
            self.content = content()
        }

        init(
            breakingSize: ContentSizeCategory = .extraExtraExtraLarge, // swiftlint:disable:this function_default_parameter_at_end
            spacing: CGFloat,
            @ViewBuilder content: () -> Content
        ) {
            self.init(
                breakingSize: breakingSize,
                hStackConfig: .init(spacing: spacing),
                vStackConfig: .init(spacing: spacing),
                content: content
            )
        }

        init(
            breakingSize: ContentSizeCategory = .extraExtraExtraLarge, // swiftlint:disable:this function_default_parameter_at_end
            hSpacing: CGFloat,
            vSpacing: CGFloat,
            @ViewBuilder content: () -> Content
        ) {
            self.init(
                breakingSize: breakingSize,
                hStackConfig: .init(spacing: hSpacing),
                vStackConfig: .init(spacing: vSpacing),
                content: content
            )
        }

        var body: some View {
            if sizeCategory > breakingSize {
                vStack
            } else {
                hStack
            }
        }

        private var hStack: some View {
            HStack(alignment: horizontalConfig.alignment, spacing: horizontalConfig.spacing) {
                content
            }
        }

        private var vStack: some View {
            VStack(alignment: verticalConfig.alignment, spacing: verticalConfig.spacing) {
                content
            }
        }
    }
    ```

### 2. IconView

- automatically scales the icon as the text size is changed
- should be used only for icons, eg. not for illustrations

!!! note

    Only "functional" icons should be scaled. Other icons that are only "decorative" should be hidden once a bigger font is applied. Recommendation is hiding after exceeding the `UIContentSizeCategory.extraExtraExtraLarge` size.

- Functional icons could be considered as icons in buttons, text inputs etc.



??? note "IconView"

    ``` swift
    import SwiftUI

    struct IconView: View {
        @ScaledMetric private var size: CGFloat

        let resource: ImageResource

        init(
            _ resource: ImageResource,
            size: CGFloat,
            relativeTo textStyle: Font.TextStyle = .body
        ) {
            self.resource = resource
            self._size = ScaledMetric(wrappedValue: size, relativeTo: textStyle)
        }

        var body: some View {
            Image(resource)
                .resizable()
                .scaledToFit()
                .frame(width: size, height: size)
        }
    }

    #Preview(traits: .sizeThatFitsLayout) {
        // FIXME: Provide valid `ImageResource` for this preview
        IconView(.Icons.checkmarkCircle, size: 24)
    }
    ```

### 3. Reducible Animation Modifier

- applies animation only when reduce motion option is turned off

??? note "ReducibleAnimationModifier"

    ``` swift
    import SwiftUI

    struct ReducibleAnimationModifier<Value: Equatable>: ViewModifier {
        @Environment(\.accessibilityReduceMotion) private var reduceMotion

        let animation: Animation
        let value: Value

        func body(content: Content) -> some View {
            content
                .animation(reduceMotion ? nil : animation, value: value)
        }
    }

    struct ReducibleAnimationModifier_Previews: PreviewProvider {
        static var previews: some View {
            Text("Hello, world!")
                .modifier(ReducibleAnimationModifier(animation: .easeOut(duration: 0.15), value: true))
        }
    }

    extension View {
        // swiftlint:disable:next function_default_parameter_at_end
        func reducibleAnimation<Value: Equatable>(_ animation: Animation = .easeInOut(duration: 0.15), value: Value) -> some View {
            modifier(ReducibleAnimationModifier(animation: animation, value: value))
        }
    }
    ```

### 4. Dynamic fonts

We already have this sorted. All you have to do is add [FuturedKit](https://github.com/futuredapp/FuturedKit) to your project and use it's `TextStyle.swift` implementation.

## Supporting VoiceOver

VoiceOver is the screen reader built into iOS that enables visually impaired users to interact with your app. Supporting it is not just a best practice; it is a fundamental requirement for EAA compliance. If a user cannot understand and operate your app with VoiceOver, it is not accessible.

Good VoiceOver support is built on a few core principles.

The Golden Rules of VoiceOver

Every interactive element MUST be accessible. VoiceOver users can only interact with what the accessibility system can see. Buttons, sliders, toggles, and all other controls must be exposed to VoiceOver.

Every element needs a clear label. If an element doesn't have a visible text title, you must provide a concise accessibilityLabel. VoiceOver should announce what the element is (e.g., "Add to cart," "User profile," "Settings").

The navigation order must be logical. The order in which VoiceOver moves through elements (by swiping left or right) should follow the visual flow of the screen. In SwiftUI, this is usually handled automatically, but you must verify it for complex layouts.

Group related information. Elements that form a single piece of information should be grouped. For example, a label "Name" and the value "John Appleseed" should be read together as one item, not two separate ones.

Core Modifiers for VoiceOver Support

SwiftUI provides a powerful set of view modifiers to implement the rules above.

??? note "Labels: What is this? (.accessibilityLabel)"

    Use this for elements without visible text, like icon-only buttons. The label should be short and descriptive. It should not include the element's type (e.g., use "Settings," not "Settings button").

    ```swift
    Button(action: { /* ... */ }) {
        Image(systemName: "gear")
    }
    .accessibilityLabel("Settings")
    ```

??? note "Traits: What kind of element is this? (.accessibilityAddTraits)"

    Traits add context by telling VoiceOver the element's role. This is crucial for custom components. For standard components like `Button` or `Toggle`, traits are often added automatically.

    -   `.isButton`: The element acts as a button.
    -   `.isHeader`: The element is a section header.
    -   `.isSelected`: The element is currently selected (e.g., a tab).
    -   `.isLink`: The element opens a URL.

    ```swift
    Text("Personal Information")
        .font(.headline)
        .accessibilityAddTraits(.isHeader)
    ```

??? note "Values: What is its current state? (.accessibilityValue)"

    Use this to communicate the current value of a control that can change, like a slider, toggle, or text field.

    ```swift
    Slider(value: $volume, in: 0...100)
        .accessibilityValue("\(Int(volume)) percent")

    Toggle("Wi-Fi", isOn: $isWifiEnabled)
        // No explicit value needed. VoiceOver reads the state
        // from the Toggle itself ("On" or "Off").
    ```

??? note "Hints: What will happen? (.accessibilityHint)"

    A hint provides extra information about the result of an action. Use them sparingly, only when the action's result isn't obvious from the label. VoiceOver reads the hint after a short pause.

    ```swift
    Button("Clear History") { /* ... */ }
        .accessibilityHint("This action cannot be undone.")
    ```

??? note "Combining & Hiding Elements"

    -   **Combining:** Use `.accessibilityElement(children: .combine)` to group multiple views into a single VoiceOver element. This is perfect for key-value pairs.
    -   **Hiding:** Use `.accessibilityHidden(true)` to hide purely decorative elements (like background patterns or illustrative flourishes) that add no value for VoiceOver users.

    ```swift
    // Combining example
    HStack {
        Text("Status:")
        Text("Confirmed")
            .font(.body.bold())
    }
    .accessibilityElement(children: .combine) // Reads: "Status: Confirmed"

    // Hiding example
    Image("decorative-background-swoosh")
        .accessibilityHidden(true)
    ```

## Testing VoiceOver

You cannot ensure VoiceOver support without testing it.

**Accessibility Inspector**: Use Xcode's built-in tool to preview labels, traits, and navigation order without leaving your Mac.

Real Device Testing: The best way to test is on an iPhone or iPad. Go to Settings > Accessibility > VoiceOver and turn it on. Navigate your app using standard VoiceOver gestures:

- Swipe Right/Left: Move to the next/previous element.

- Double-Tap: Activate the selected element (tap a button).

- Three-Finger Swipe: Scroll.

## Beyond the App UI

- EAA compliance extends beyond just the application's interface.
- Provide Accessible Support: Your customer support channels (like helpdesks, chat, email, and phone support) must also be accessible.
- Maintain Documentation: You must prepare and maintain documentation that describes how your product or service meets the accessibility requirements. This often takes the form of an Accessibility Statement.

## Sources

- [Apple's Official Accessibility Documentation](https://developer.apple.com/accessibility/)
- [WCAG 2.1 Guidelines (W3C)](https://www.w3.org/TR/WCAG21/)
- [Hacking with Swift - Accessibility](https://www.hackingwithswift.com/book/ios-swiftui/accessibility-introduction)
- [understanding-the-european-accessibility-act-eaa](https://www.browserstack.com/blog/understanding-the-european-accessibility-act-eaa/)
- [mobilea11y blog](https://mobilea11y.com/blog/european-accessibility-act/)