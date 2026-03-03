---
name: ios-design-guidelines
description: Apple Human Interface Guidelines for iPhone. Use when building, reviewing, or refactoring SwiftUI/UIKit interfaces for iOS. Triggers on tasks involving iPhone UI, iOS components, accessibility, Dynamic Type, Dark Mode, or HIG compliance.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# iOS Design Guidelines for iPhone

Comprehensive rules derived from Apple's Human Interface Guidelines. Apply these when building, reviewing, or refactoring any iPhone app interface.

---

## 1. Layout & Safe Areas
**Impact:** CRITICAL

### Rule 1.1: Minimum 44pt Touch Targets
All interactive elements must have a minimum tap target of 44x44 points. This includes buttons, links, toggles, and custom controls.

**Correct:**
```swift
Button("Save") { save() }
    .frame(minWidth: 44, minHeight: 44)
```

**Incorrect:**
```swift
// 20pt icon with no padding — too small to tap reliably
Button(action: save) {
    Image(systemName: "checkmark")
        .font(.system(size: 20))
}
// Missing .frame(minWidth: 44, minHeight: 44)
```

### Rule 1.2: Respect Safe Areas
Never place interactive or essential content under the status bar, Dynamic Island, or home indicator. Use SwiftUI's automatic safe area handling or UIKit's `safeAreaLayoutGuide`.

**Correct:**
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Content")
        }
        // SwiftUI respects safe areas by default
    }
}
```

**Incorrect:**
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Content")
        }
        .ignoresSafeArea() // Content will be clipped under notch/Dynamic Island
    }
}
```

Use `.ignoresSafeArea()` only for background fills, images, or decorative elements — never for text or interactive controls.

### Rule 1.3: Primary Actions in the Thumb Zone
Place primary actions at the bottom of the screen where the user's thumb naturally rests. Secondary actions and navigation belong at the top.

**Correct:**
```swift
VStack {
    ScrollView { /* content */ }
    Button("Continue") { next() }
        .buttonStyle(.borderedProminent)
        .padding()
}
```

**Incorrect:**
```swift
VStack {
    Button("Continue") { next() } // Top of screen — hard to reach one-handed
        .buttonStyle(.borderedProminent)
        .padding()
    ScrollView { /* content */ }
}
```

### Rule 1.4: Support All iPhone Screen Sizes
Design for iPhone SE (375pt wide) through iPhone Pro Max (430pt wide). Use flexible layouts, avoid hardcoded widths.

**Correct:**
```swift
HStack(spacing: 12) {
    ForEach(items) { item in
        CardView(item: item)
            .frame(maxWidth: .infinity) // Adapts to screen width
    }
}
```

**Incorrect:**
```swift
HStack(spacing: 12) {
    ForEach(items) { item in
        CardView(item: item)
            .frame(width: 180) // Breaks on SE, wastes space on Pro Max
    }
}
```

### Rule 1.5: 8pt Grid Alignment
Align spacing, padding, and element sizes to multiples of 8 points (8, 16, 24, 32, 40, 48). Use 4pt for fine adjustments.

### Rule 1.6: Landscape Support
Support landscape orientation unless the app is task-specific (e.g., camera). Use `ViewThatFits` or `GeometryReader` for adaptive layouts.

---

## 2. Navigation
**Impact:** CRITICAL

### Rule 2.1: Tab Bar for Top-Level Sections
Use a tab bar at the bottom of the screen for 3 to 5 top-level sections. Each tab should represent a distinct category of content or functionality.

**Correct:**
```swift
TabView {
    HomeView()
        .tabItem {
            Label("Home", systemImage: "house")
        }
    SearchView()
        .tabItem {
            Label("Search", systemImage: "magnifyingglass")
        }
    ProfileView()
        .tabItem {
            Label("Profile", systemImage: "person")
        }
}
```

**Incorrect:**
```swift
// Hamburger menu hidden behind three lines — discoverability is near zero
NavigationView {
    Button(action: { showMenu.toggle() }) {
        Image(systemName: "line.horizontal.3")
    }
}
```

### Rule 2.2: Never Use Hamburger Menus
Hamburger (drawer) menus hide navigation, reduce discoverability, and violate iOS conventions. Use a tab bar instead. If you have more than 5 sections, consolidate or use a "More" tab.

### Rule 2.3: Large Titles in Primary Views
Use `.navigationBarTitleDisplayMode(.large)` for top-level views. Titles transition to inline (`.inline`) when the user scrolls.

**Correct:**
```swift
NavigationStack {
    List(items) { item in
        ItemRow(item: item)
    }
    .navigationTitle("Messages")
    .navigationBarTitleDisplayMode(.large)
}
```

### Rule 2.4: Never Override Back Swipe
The swipe-from-left-edge gesture for back navigation is a system-level expectation. Never attach custom gesture recognizers that interfere with it.

**Incorrect:**
```swift
.gesture(
    DragGesture()
        .onChanged { /* custom drawer */ } // Conflicts with system back swipe
)
```

### Rule 2.5: Use NavigationStack for Hierarchical Content
Use `NavigationStack` (not the deprecated `NavigationView`) for drill-down content. Use `NavigationPath` for programmatic navigation.

**Correct:**
```swift
NavigationStack(path: $path) {
    List(items) { item in
        NavigationLink(value: item) {
            ItemRow(item: item)
        }
    }
    .navigationDestination(for: Item.self) { item in
        ItemDetail(item: item)
    }
}
```

### Rule 2.6: Preserve State Across Navigation
When users navigate back and then forward, or switch tabs, restore the previous scroll position and input state. Use `@SceneStorage` or `@State` to persist view state.

---

## 3. Typography & Dynamic Type
**Impact:** HIGH

### Rule 3.1: Use Built-in Text Styles
Always use semantic text styles rather than hardcoded sizes. These scale automatically with Dynamic Type.

**Correct:**
```swift
VStack(alignment: .leading, spacing: 4) {
    Text("Section Title")
        .font(.headline)
    Text("Body content that explains the section.")
        .font(.body)
    Text("Last updated 2 hours ago")
        .font(.caption)
        .foregroundStyle(.secondary)
}
```

**Incorrect:**
```swift
VStack(alignment: .leading, spacing: 4) {
    Text("Section Title")
        .font(.system(size: 17, weight: .semibold)) // Won't scale with Dynamic Type
    Text("Body content")
        .font(.system(size: 15)) // Won't scale with Dynamic Type
}
```

### Rule 3.2: Support Dynamic Type Including Accessibility Sizes
Dynamic Type can scale text up to approximately 200% at the largest accessibility sizes. Layouts must reflow — never truncate or clip essential text.

**Correct:**
```swift
HStack {
    Image(systemName: "star")
    Text("Favorites")
        .font(.body)
}
// At accessibility sizes, consider using ViewThatFits or
// AnyLayout to switch from HStack to VStack
```

Use `@Environment(\.dynamicTypeSize)` to detect size category and adapt layouts:

```swift
@Environment(\.dynamicTypeSize) var dynamicTypeSize

var body: some View {
    if dynamicTypeSize.isAccessibilitySize {
        VStack { content }
    } else {
        HStack { content }
    }
}
```

### Rule 3.3: Custom Fonts Must Use UIFontMetrics
If you use a custom typeface, scale it with `UIFontMetrics` so it responds to Dynamic Type.

**Correct:**
```swift
extension Font {
    static func scaledCustom(size: CGFloat, relativeTo textStyle: Font.TextStyle) -> Font {
        .custom("CustomFont-Regular", size: size, relativeTo: textStyle)
    }
}

// Usage
Text("Hello")
    .font(.scaledCustom(size: 17, relativeTo: .body))
```

### Rule 3.4: SF Pro as System Font
Use the system font (SF Pro) unless brand requirements dictate otherwise. SF Pro is optimized for legibility on Apple displays.

### Rule 3.5: Minimum 11pt Text
Never display text smaller than 11pt. Prefer 17pt for body text. Use the `caption2` style (11pt) as the absolute minimum.

### Rule 3.6: Hierarchy Through Weight and Size
Establish visual hierarchy through font weight and size. Do not rely solely on color to differentiate text levels.

---

## 4. Color & Dark Mode
**Impact:** HIGH

### Rule 4.1: Use Semantic System Colors
Use system-provided semantic colors that automatically adapt to light and dark modes.

**Correct:**
```swift
Text("Primary text")
    .foregroundStyle(.primary) // Adapts to light/dark

Text("Secondary info")
    .foregroundStyle(.secondary)

VStack { }
    .background(Color(.systemBackground)) // White in light, black in dark
```

**Incorrect:**
```swift
Text("Primary text")
    .foregroundColor(.black) // Invisible on dark backgrounds

VStack { }
    .background(.white) // Blinding in Dark Mode
```

### Rule 4.2: Provide Light and Dark Variants for Custom Colors
Define custom colors in the asset catalog with both Any Appearance and Dark Appearance variants.

```swift
// In Assets.xcassets, define "BrandBlue" with:
// Any Appearance: #0066CC
// Dark Appearance: #4DA3FF

Text("Brand text")
    .foregroundStyle(Color("BrandBlue")) // Automatically switches
```

### Rule 4.3: Never Rely on Color Alone
Always pair color with text, icons, or shapes to convey meaning. Approximately 8% of men have some form of color vision deficiency.

**Correct:**
```swift
HStack {
    Image(systemName: "exclamationmark.triangle.fill")
        .foregroundStyle(.red)
    Text("Error: Invalid email address")
        .foregroundStyle(.red)
}
```

**Incorrect:**
```swift
// Only color indicates the error — invisible to colorblind users
TextField("Email", text: $email)
    .border(isValid ? .green : .red)
```

### Rule 4.4: 4.5:1 Contrast Ratio Minimum
All text must meet WCAG AA contrast ratios: 4.5:1 for normal text, 3:1 for large text (18pt+ or 14pt+ bold).

### Rule 4.5: Support Display P3 Wide Gamut
Use Display P3 color space for vibrant, accurate colors on modern iPhones. Define colors in the asset catalog with the Display P3 gamut.

### Rule 4.6: Background Hierarchy
Use the three-level background hierarchy for depth:
- `systemBackground` — primary surface
- `secondarySystemBackground` — grouped content, cards
- `tertiarySystemBackground` — elements within grouped content

### Rule 4.7: One Accent Color for Interactive Elements
Choose a single tint/accent color for all interactive elements (buttons, links, toggles). This creates a consistent, learnable visual language.

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .tint(.indigo) // All interactive elements use indigo
        }
    }
}
```

---

## 5. Accessibility
**Impact:** CRITICAL

### Rule 5.1: VoiceOver Labels on All Interactive Elements
Every button, control, and interactive element must have a meaningful accessibility label.

**Correct:**
```swift
Button(action: addToCart) {
    Image(systemName: "cart.badge.plus")
}
.accessibilityLabel("Add to cart")
```

**Incorrect:**
```swift
Button(action: addToCart) {
    Image(systemName: "cart.badge.plus")
}
// VoiceOver reads "cart.badge.plus" — meaningless to users
```

### Rule 5.2: Logical VoiceOver Navigation Order
Ensure VoiceOver reads elements in a logical order. Use `.accessibilitySortPriority()` to adjust when the visual layout doesn't match the reading order.

```swift
VStack {
    Text("Price: $29.99")
        .accessibilitySortPriority(1) // Read first
    Text("Product Name")
        .accessibilitySortPriority(2) // Read second
}
```

### Rule 5.3: Support Bold Text
When the user enables Bold Text in Settings, use the `.bold` dynamic type variants. SwiftUI text styles handle this automatically. Custom text must respond to `UIAccessibility.isBoldTextEnabled`.

### Rule 5.4: Support Reduce Motion
Disable decorative animations and parallax when Reduce Motion is enabled. Use `@Environment(\.accessibilityReduceMotion)`.

**Correct:**
```swift
@Environment(\.accessibilityReduceMotion) var reduceMotion

var body: some View {
    CardView()
        .animation(reduceMotion ? nil : .spring(), value: isExpanded)
}
```

### Rule 5.5: Support Increase Contrast
When the user enables Increase Contrast, ensure custom colors have higher-contrast variants. Use `@Environment(\.colorSchemeContrast)` to detect.

### Rule 5.6: Don't Convey Info Only by Color, Shape, or Position
Information must be available through multiple channels. Pair visual indicators with text or accessibility descriptions.

### Rule 5.7: Alternative Interactions for All Gestures
Every custom gesture must have an equivalent tap-based or menu-based alternative for users who cannot perform complex gestures.

### Rule 5.8: Support Switch Control and Full Keyboard Access
Ensure all interactions work with Switch Control (external switches) and Full Keyboard Access (Bluetooth keyboards). Test navigation order and focus behavior.

---

## 6. Gestures & Input
**Impact:** HIGH

### Rule 6.1: Use Standard Gestures
Use the standard iOS gesture vocabulary: tap, long press, swipe, pinch, rotate. Users already understand these.

| Gesture | Standard Use |
|---------|-------------|
| Tap | Primary action, selection |
| Long press | Context menu, preview |
| Swipe horizontal | Delete, archive, navigate back |
| Swipe vertical | Scroll, dismiss sheet |
| Pinch | Zoom in/out |
| Two-finger rotate | Rotate content |

### Rule 6.2: Never Override System Gestures
These gestures are reserved by the system and must not be intercepted:
- Swipe from left edge (back navigation)
- Swipe down from top-left (Notification Center)
- Swipe down from top-right (Control Center)
- Swipe up from bottom (home / app switcher)

### Rule 6.3: Custom Gestures Must Be Discoverable
If you add a custom gesture, provide visual hints (e.g., a grabber handle) and ensure the action is also available through a visible button or menu item.

### Rule 6.4: Support All Input Methods
Design for touch first, but also support:
- Hardware keyboards (iPad keyboard accessories, Bluetooth keyboards)
- Assistive devices (Switch Control, head tracking)
- Pointer input (assistive touch)

---

## 7. Components
**Impact:** HIGH

### Rule 7.1: Button Styles
Use the built-in button styles appropriately:
- `.borderedProminent` — primary call-to-action
- `.bordered` — secondary actions
- `.borderless` — tertiary or inline actions
- `.destructive` role — red tint for delete/remove

**Correct:**
```swift
VStack(spacing: 16) {
    Button("Purchase") { buy() }
        .buttonStyle(.borderedProminent)

    Button("Add to Wishlist") { wishlist() }
        .buttonStyle(.bordered)

    Button("Delete", role: .destructive) { delete() }
}
```

### Rule 7.2: Alerts — Critical Info Only
Use alerts sparingly for critical information that requires a decision. Prefer 2 buttons; maximum 3. The destructive option should use `.destructive` role.

**Correct:**
```swift
.alert("Delete Photo?", isPresented: $showAlert) {
    Button("Delete", role: .destructive) { deletePhoto() }
    Button("Cancel", role: .cancel) { }
} message: {
    Text("This photo will be permanently removed.")
}
```

**Incorrect:**
```swift
// Alert for non-critical info — should be a banner or toast
.alert("Tip", isPresented: $showTip) {
    Button("OK") { }
} message: {
    Text("Swipe left to delete items.")
}
```

### Rule 7.3: Sheets for Scoped Tasks
Present sheets for self-contained tasks. Always provide a way to dismiss (close button or swipe down). Use `.presentationDetents()` for half-height sheets.

```swift
.sheet(isPresented: $showCompose) {
    NavigationStack {
        ComposeView()
            .navigationTitle("New Message")
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancel") { showCompose = false }
                }
                ToolbarItem(placement: .confirmationAction) {
                    Button("Send") { send() }
                }
            }
    }
    .presentationDetents([.medium, .large])
}
```

### Rule 7.4: Lists — Inset Grouped Default
Use the `.insetGrouped` list style as the default. Support swipe actions for common operations. Minimum row height is 44pt.

**Correct:**
```swift
List {
    Section("Recent") {
        ForEach(recentItems) { item in
            ItemRow(item: item)
                .swipeActions(edge: .trailing) {
                    Button(role: .destructive) { delete(item) } label: {
                        Label("Delete", systemImage: "trash")
                    }
                    Button { archive(item) } label: {
                        Label("Archive", systemImage: "archivebox")
                    }
                    .tint(.blue)
                }
        }
    }
}
.listStyle(.insetGrouped)
```

### Rule 7.5: Tab Bar Behavior
- Use SF Symbols for tab icons — filled variant for the selected tab, outline for unselected
- Never hide the tab bar when navigating deeper within a tab
- Badge important counts with `.badge()`

```swift
TabView {
    MessagesView()
        .tabItem {
            Label("Messages", systemImage: "message")
        }
        .badge(unreadCount)
}
```

### Rule 7.6: Search
Place search using `.searchable()`. Provide search suggestions and support recent searches.

```swift
NavigationStack {
    List(filteredItems) { item in
        ItemRow(item: item)
    }
    .searchable(text: $searchText, prompt: "Search items")
    .searchSuggestions {
        ForEach(suggestions) { suggestion in
            Text(suggestion.title)
                .searchCompletion(suggestion.title)
        }
    }
}
```

### Rule 7.7: Context Menus
Use context menus (long press) for secondary actions. Never use a context menu as the only way to access an action.

```swift
PhotoView(photo: photo)
    .contextMenu {
        Button { share(photo) } label: {
            Label("Share", systemImage: "square.and.arrow.up")
        }
        Button { favorite(photo) } label: {
            Label("Favorite", systemImage: "heart")
        }
        Button(role: .destructive) { delete(photo) } label: {
            Label("Delete", systemImage: "trash")
        }
    }
```

### Rule 7.8: Progress Indicators
- Determinate (`ProgressView(value:total:)`) for operations with known duration
- Indeterminate (`ProgressView()`) for unknown duration
- Never block the entire screen with a spinner

---

## 8. Patterns
**Impact:** MEDIUM

### Rule 8.1: Onboarding — Max 3 Pages, Skippable
Keep onboarding to 3 or fewer pages. Always provide a skip option. Defer sign-in until the user needs authenticated features.

```swift
TabView {
    OnboardingPage(
        image: "wand.and.stars",
        title: "Smart Suggestions",
        subtitle: "Get personalized recommendations based on your preferences."
    )
    OnboardingPage(
        image: "bell.badge",
        title: "Stay Updated",
        subtitle: "Receive notifications for things that matter to you."
    )
    OnboardingPage(
        image: "checkmark.shield",
        title: "Private & Secure",
        subtitle: "Your data stays on your device."
    )
}
.tabViewStyle(.page)
.overlay(alignment: .topTrailing) {
    Button("Skip") { completeOnboarding() }
        .padding()
}
```

### Rule 8.2: Loading — Skeleton Views, No Blocking Spinners
Use skeleton/placeholder views that match the layout of the content being loaded. Never show a full-screen blocking spinner.

**Correct:**
```swift
if isLoading {
    ForEach(0..<5) { _ in
        SkeletonRow() // Placeholder matching final row layout
            .redacted(reason: .placeholder)
    }
} else {
    ForEach(items) { item in
        ItemRow(item: item)
    }
}
```

**Incorrect:**
```swift
if isLoading {
    ProgressView("Loading...") // Blocks the entire view
} else {
    List(items) { item in ItemRow(item: item) }
}
```

### Rule 8.3: Launch Screen — Match First Screen
The launch storyboard must visually match the initial screen of the app. No splash logos, no branding screens. This creates the perception of instant launch.

### Rule 8.4: Modality — Use Sparingly
Present modal views only when the user must complete or abandon a focused task. Always provide a clear dismiss action. Never stack modals on top of modals.

### Rule 8.5: Notifications — High Value Only
Only send notifications for content the user genuinely cares about. Support actionable notifications. Categorize notifications so users can control them granularly.

### Rule 8.6: Settings Placement
- **Frequent settings:** In-app settings screen accessible from a profile or gear icon
- **Privacy/permission settings:** Defer to the system Settings app via URL scheme
- Never duplicate system-level controls in-app

### Rule 8.7: Feedback — Visual + Haptic
Provide immediate feedback for every user action:
- Visual state change (button highlight, animation)
- Haptic feedback for significant actions using `UIImpactFeedbackGenerator`, `UINotificationFeedbackGenerator`, or `UISelectionFeedbackGenerator`

```swift
Button("Complete") {
    let generator = UINotificationFeedbackGenerator()
    generator.notificationOccurred(.success)
    completeTask()
}
```

---

## 9. Privacy & Permissions
**Impact:** HIGH

### Rule 9.1: Request Permissions in Context
Request a permission at the moment the user takes an action that needs it — never at app launch.

**Correct:**
```swift
Button("Take Photo") {
    // Request camera permission only when the user taps this button
    AVCaptureDevice.requestAccess(for: .video) { granted in
        if granted { showCamera = true }
    }
}
```

**Incorrect:**
```swift
// In AppDelegate.didFinishLaunching — too early, no context
func application(_ application: UIApplication, didFinishLaunchingWithOptions ...) {
    AVCaptureDevice.requestAccess(for: .video) { _ in }
    CLLocationManager().requestWhenInUseAuthorization()
    UNUserNotificationCenter.current().requestAuthorization(options: [.alert]) { _, _ in }
}
```

### Rule 9.2: Explain Before System Prompt
Show a custom explanation screen before triggering the system permission dialog. The system dialog only appears once — if the user denies, the app must direct them to Settings.

```swift
struct LocationExplanation: View {
    var body: some View {
        VStack(spacing: 16) {
            Image(systemName: "location.fill")
                .font(.largeTitle)
            Text("Find Nearby Stores")
                .font(.headline)
            Text("We use your location to show stores within walking distance. Your location is never shared or stored.")
                .font(.body)
                .multilineTextAlignment(.center)
            Button("Enable Location") {
                locationManager.requestWhenInUseAuthorization()
            }
            .buttonStyle(.borderedProminent)
            Button("Not Now") { dismiss() }
                .foregroundStyle(.secondary)
        }
        .padding()
    }
}
```

### Rule 9.3: Support Sign in with Apple
If the app offers any third-party sign-in (Google, Facebook), it must also offer Sign in with Apple. Present it as the first option.

### Rule 9.4: Don't Require Accounts Unless Necessary
Let users explore the app before requiring sign-in. Gate only features that genuinely need authentication (purchases, sync, social features).

### Rule 9.5: App Tracking Transparency
If you track users across apps or websites, display the ATT prompt. Respect denial — do not degrade the experience for users who opt out.

### Rule 9.6: Location Button for One-Time Access
Use `LocationButton` for actions that need location once without requesting ongoing permission.

```swift
LocationButton(.currentLocation) {
    fetchNearbyStores()
}
.labelStyle(.titleAndIcon)
```

---

## 10. System Integration
**Impact:** MEDIUM

### Rule 10.1: Widgets for Glanceable Data
Provide widgets using WidgetKit for information users check frequently. Widgets are not interactive (beyond tapping to open the app), so show the most useful snapshot.

### Rule 10.2: App Shortcuts for Key Actions
Define App Shortcuts so users can trigger key actions from Siri, Spotlight, and the Shortcuts app.

```swift
struct MyAppShortcuts: AppShortcutsProvider {
    static var appShortcuts: [AppShortcut] {
        AppShortcut(
            intent: StartWorkoutIntent(),
            phrases: ["Start a workout in \(.applicationName)"],
            shortTitle: "Start Workout",
            systemImageName: "figure.run"
        )
    }
}
```

### Rule 10.3: Spotlight Indexing
Index app content with `CSSearchableItem` so users can find it from Spotlight search.

### Rule 10.4: Share Sheet Integration
Support the system share sheet for content that users might want to send elsewhere. Implement `UIActivityItemSource` or use `ShareLink` in SwiftUI.

```swift
ShareLink(item: article.url) {
    Label("Share", systemImage: "square.and.arrow.up")
}
```

### Rule 10.5: Live Activities
Use Live Activities and the Dynamic Island for real-time, time-bound events (delivery tracking, sports scores, workouts).

### Rule 10.6: Handle Interruptions Gracefully
Save state and pause gracefully when interrupted by:
- Phone calls
- Siri invocations
- Notifications
- App switcher
- FaceTime SharePlay

Use `scenePhase` to detect transitions:

```swift
@Environment(\.scenePhase) var scenePhase

.onChange(of: scenePhase) { _, newPhase in
    switch newPhase {
    case .active: resumeActivity()
    case .inactive: pauseActivity()
    case .background: saveState()
    @unknown default: break
    }
}
```

---

## Quick Reference

| Need | Component | Notes |
|------|-----------|-------|
| Top-level sections (3-5) | `TabView` with `.tabItem` | Bottom tab bar, SF Symbols |
| Hierarchical drill-down | `NavigationStack` | Large title on root, inline on children |
| Self-contained task | `.sheet` | Swipe to dismiss, cancel/done buttons |
| Critical decision | `.alert` | 2 buttons preferred, max 3 |
| Secondary actions | `.contextMenu` | Long press; must also be accessible elsewhere |
| Scrolling content | `List` with `.insetGrouped` | 44pt min row, swipe actions |
| Text input | `TextField` / `TextEditor` | Label above, validation below |
| Selection (few options) | `Picker` | Segmented for 2-5, wheel for many |
| Selection (on/off) | `Toggle` | Aligned right in a list row |
| Search | `.searchable` | Suggestions, recent searches |
| Progress (known) | `ProgressView(value:total:)` | Show percentage or time remaining |
| Progress (unknown) | `ProgressView()` | Inline, never full-screen blocking |
| One-time location | `LocationButton` | No persistent permission needed |
| Sharing content | `ShareLink` | System share sheet |
| Haptic feedback | `UIImpactFeedbackGenerator` | `.light`, `.medium`, `.heavy` |
| Destructive action | `Button(role: .destructive)` | Red tint, confirm via alert |

---

## Evaluation Checklist

Use this checklist to audit an iPhone app for HIG compliance:

### Layout & Safe Areas
- [ ] All touch targets are at least 44x44pt
- [ ] No content is clipped under status bar, Dynamic Island, or home indicator
- [ ] Primary actions are in the bottom half of the screen (thumb zone)
- [ ] Layout adapts from iPhone SE to Pro Max without breaking
- [ ] Spacing aligns to the 8pt grid

### Navigation
- [ ] Tab bar is used for 3-5 top-level sections
- [ ] No hamburger/drawer menus
- [ ] Primary views use large titles
- [ ] Swipe-from-left-edge back navigation works throughout
- [ ] State is preserved when switching tabs

### Typography
- [ ] All text uses built-in text styles or `UIFontMetrics`-scaled custom fonts
- [ ] Dynamic Type is supported up to accessibility sizes
- [ ] Layouts reflow at large text sizes (no truncation of essential text)
- [ ] Minimum text size is 11pt

### Color & Dark Mode
- [ ] App uses semantic system colors or provides light/dark asset variants
- [ ] Dark Mode looks intentional (not just inverted)
- [ ] No information conveyed by color alone
- [ ] Text contrast meets 4.5:1 (normal) or 3:1 (large)
- [ ] Single accent color for interactive elements

### Accessibility
- [ ] VoiceOver reads all screens logically with meaningful labels
- [ ] Bold Text preference is respected
- [ ] Reduce Motion disables decorative animations
- [ ] Increase Contrast variant exists for custom colors
- [ ] All gestures have alternative access paths

### Components
- [ ] Alerts are used only for critical decisions
- [ ] Sheets have a dismiss path (button and/or swipe)
- [ ] List rows are at least 44pt tall
- [ ] Tab bar is never hidden during navigation
- [ ] Destructive buttons use the `.destructive` role

### Privacy
- [ ] Permissions are requested in context, not at launch
- [ ] Custom explanation shown before each system permission dialog
- [ ] Sign in with Apple offered alongside other providers
- [ ] App is usable without an account for basic features
- [ ] ATT prompt is shown if tracking, and denial is respected

### System Integration
- [ ] Widgets show glanceable, up-to-date information
- [ ] App content is indexed for Spotlight
- [ ] Share Sheet is available for shareable content
- [ ] App handles interruptions (calls, background, Siri) gracefully

---

## Anti-Patterns

These are common mistakes that violate the iOS Human Interface Guidelines. Never do these:

1. **Hamburger menus** — Use a tab bar. Hamburger menus hide navigation and reduce feature discoverability by up to 50%.

2. **Custom back buttons that break swipe-back** — If you replace the back button, ensure the swipe-from-left-edge gesture still works via `NavigationStack`.

3. **Full-screen blocking spinners** — Use skeleton views or inline progress indicators. Blocking spinners make the app feel frozen.

4. **Splash screens with logos** — The launch screen must mirror the first screen of the app. Branding delays feel artificial.

5. **Requesting all permissions at launch** — Asking for camera, location, notifications, and contacts on first launch guarantees most will be denied.

6. **Hardcoded font sizes** — Use text styles. Hardcoded sizes ignore Dynamic Type and accessibility preferences, breaking the app for millions of users.

7. **Using only color to indicate state** — Red/green for valid/invalid excludes colorblind users. Always pair with icons or text.

8. **Alerts for non-critical information** — Alerts interrupt flow and require dismissal. Use banners, toasts, or inline messages for tips and non-critical information.

9. **Hiding the tab bar on push** — Tab bars should remain visible throughout navigation within a tab. Hiding them disorients users.

10. **Ignoring safe areas** — Using `.ignoresSafeArea()` on content views causes text and buttons to disappear under the notch, Dynamic Island, or home indicator.

11. **Non-dismissable modals** — Every modal must have a clear dismiss path (close button, cancel, swipe down). Trapping users in a modal is hostile.

12. **Custom gestures without alternatives** — A three-finger swipe for undo is unusable for many people. Provide a visible button or menu item as well.

13. **Tiny touch targets** — Buttons and links smaller than 44pt cause mis-taps, especially in lists and toolbars.

14. **Stacked modals** — Presenting a sheet on top of a sheet on top of a sheet creates navigation confusion. Use navigation within a single modal instead.

15. **Dark Mode as an afterthought** — Using hardcoded colors means the app is either broken in Dark Mode or light mode. Always use semantic colors.
