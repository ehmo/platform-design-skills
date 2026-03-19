---
name: tvos-design-guidelines
description: Apple Human Interface Guidelines for Apple TV. Use when building tvOS apps with focus-based navigation, Siri Remote input, or living room viewing experiences. Triggers on tasks involving Apple TV, tvOS, 10-foot UI, or media playback.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# tvOS Design Guidelines

Apple TV is a living room device driven entirely by focus-based navigation and the Siri Remote. There is no pointer, no touch screen, and no mouse. Every design decision must account for the 10-foot viewing distance, the simplicity of the remote, and the lean-back nature of TV consumption.

---

## 1. Focus-Based Navigation (CRITICAL)

The focus system is the foundation of all tvOS interaction. There is no cursor -- users move focus between elements using the Siri Remote touch surface.

### Rules

**FOCUS-01: Every interactive element must have a clearly visible focus state.**
The focused item must be unmistakably distinguished from unfocused items. Use scaling (typically 1.05x-1.1x), elevation via shadow, brightness changes, or border highlights. Never rely on color alone.

**FOCUS-02: Focus movement must be predictable and follow a logical spatial layout.**
When a user swipes right, focus must move to the element visually to the right. Avoid layouts where focus jumps unexpectedly across the screen. Grid and linear layouts are safest.

**FOCUS-03: Use focus guides (UIFocusGuide) to bridge gaps in layouts.**
When visual gaps exist between focusable elements, add invisible focus guides so the user does not get stuck. Every swipe should move focus somewhere meaningful.

**FOCUS-04: Apply the parallax effect to focused items.**
Focused cards, posters, and icons should exhibit a subtle parallax tilt responding to touch surface movement. Use layered images (LSR format) with foreground, midground, and background layers. This communicates depth and confirms focus.

**Correct:**
```swift
// SwiftUI — custom focus engine with explicit focus state
struct ContentView: View {
    @FocusState private var focusedItem: String?

    var body: some View {
        HStack(spacing: 40) {
            ForEach(items) { item in
                CardView(item: item)
                    .focusable()
                    .focused($focusedItem, equals: item.id)
                    .scaleEffect(focusedItem == item.id ? 1.1 : 1.0)
                    .shadow(radius: focusedItem == item.id ? 20 : 0)
                    .animation(.easeInOut(duration: 0.15), value: focusedItem)
            }
        }
    }
}
```

**Incorrect:**
```swift
// SwiftUI — no focus state: unfocused and focused items look identical
struct ContentView: View {
    var body: some View {
        HStack(spacing: 40) {
            ForEach(items) { item in
                CardView(item: item)
                    .focusable()
                // No scale, shadow, or visual change on focus
                // User cannot tell which item is selected
            }
        }
    }
}
```

**FOCUS-05: Make focus targets large enough for comfortable navigation.**
Minimum recommended touch target is 250x150pt for cards. Smaller elements are difficult to land on with swipe-based navigation. Group small actions under a focused parent when possible.

**FOCUS-06: Provide a default focused element on every screen.**
When a view appears, one element must already hold focus. Choose the most likely user intent -- typically the primary content item or the first item in a collection.

**FOCUS-07: Preserve focus memory when returning to a screen.**
If a user navigates away and returns, focus should restore to the last focused item on that screen, not reset to the default.

**FOCUS-08: Never trap focus.**
Users must always be able to move focus away from any element. If focus cannot leave a region, the app feels broken.

**FOCUS-09: Reduce re-orientation cost.**
Keep row order stable, restore prior focus when returning, and prefer nearby focus destinations so users do not have to rescan the entire screen after each navigation step.

### Parallax Layer Reference

| Layer | Purpose | Movement Amount |
|-------|---------|-----------------|
| Background | Static backdrop, blurred imagery | Minimal (1-2pt) |
| Midground | Primary artwork or content image | Moderate (3-5pt) |
| Foreground | Title text, logos, badges | Maximum (5-8pt) |

Use Xcode's LSR (Layered Static Image) format for static layered images in the asset catalog — the system animates them automatically on focus. For custom programmatic parallax, stack `UIImageView` instances and use the focus engine callbacks (`didUpdateFocus(in:with:)` and `UIFocusAnimationCoordinator`) to drive layer movement during focus transitions. (`UIMotionEffect` responds only to subtle Siri Remote gyroscope micromotion and is not the mechanism for focus-driven parallax.)

---

## 2. Siri Remote (CRITICAL)

The Siri Remote is the primary (and often only) input device. It has a touch surface, Menu button, Play/Pause button, Siri/microphone button, volume buttons, and a power button.

### Rules

**REMOTE-01: Touch surface swipes control focus movement.**
Swiping moves focus in the corresponding direction. Clicking the touch surface selects the focused item. These are the two fundamental interactions -- design everything around them.

**REMOTE-02: Menu button must always navigate back.**
Pressing Menu should dismiss the current screen, close an overlay, or navigate up the hierarchy. At the top level, it returns to the Apple TV home screen. Never override this expectation.

**REMOTE-03: Play/Pause button must control media playback.**
If media is playing, Play/Pause should toggle playback regardless of what screen is visible. Do not repurpose this button for non-media actions.

**REMOTE-04: Never require complex or multi-finger gestures.**
The Siri Remote touch surface is small. Do not require pinch, rotate, multi-tap, or long-press gestures. Stick to single-finger swipe and click.

**REMOTE-05: Swipe directions must be intuitive and consistent.**
Horizontal swipes scroll horizontally; vertical swipes scroll vertically. Never invert axes. Diagonal content movement should follow the dominant swipe axis.

**REMOTE-06: Support Siri voice input for search and text entry.**
Text input via the on-screen keyboard is tedious on tvOS. Always support dictation and Siri search as alternatives to keyboard entry.

**REMOTE-07: Provide click feedback.**
When the user clicks the touch surface to select an item, provide immediate visual feedback (animation, highlight change, or haptic-style visual pulse) so the click feels responsive.

**REMOTE-08: Never make the on-screen keyboard the only practical text path.**
For search, sign-in, and setup, prefer dictation, recent queries, autofill, or short code-based flows over long remote-typed text. Remote text entry carries high motor and cognitive cost.

---

## 3. 10-Foot UI (HIGH)

Apple TV content is viewed from across a room, typically 8-12 feet (2.5-3.5 meters) from the screen. All visual design must account for this distance.

### Rules

**DISTANCE-01: Minimum body text size is 29pt.**
Text below 29pt becomes difficult to read at living room distances. Titles should be 48pt or larger. Use San Francisco Display or comparable high-legibility typeface.

**DISTANCE-02: Maintain high contrast between text and backgrounds.**
Use light text on dark backgrounds as the default. tvOS uses a dark theme. Contrast ratio should meet WCAG AA or higher (4.5:1 for body text, 3:1 for large text).

**DISTANCE-03: Limit text per screen.**
TV is a visual medium. Show headlines, short descriptions, and metadata -- not paragraphs. If extended text is necessary, use scrollable text overlays that the user explicitly opens.

**DISTANCE-04: Use bold, clear imagery at high resolution.**
Full-screen background images should be 1920x1080 or 3840x2160 (4K). Content artwork should be sharp and visually engaging. Avoid small, detailed illustrations that lose clarity at distance.

**DISTANCE-05: Keep layouts simple and spacious.**
Generous margins and padding. Do not crowd the screen with many small elements. A single row of 5-7 cards is preferable to a dense grid of 20+ thumbnails.

**DISTANCE-06: Use the TV-safe area.**
Keep all critical content within the safe area (60pt inset from edges). Content near screen edges may be cropped on some TV sets due to overscan.

**DISTANCE-07: Avoid thin fonts and hairline borders.**
Thin strokes disappear on TV displays, especially with motion blur and compression artifacts. Use medium or semibold weights minimum.

### Text Size Reference

| Element | Minimum Size | Recommended Size |
|---------|-------------|-----------------|
| Body text | 29pt | 31-35pt |
| Secondary labels | 25pt | 29pt |
| Titles | 48pt | 52-76pt |
| Large headers | 64pt | 76-96pt |
| Buttons | 29pt | 35-38pt |

---

## 4. Top Shelf (HIGH)

The Top Shelf is a prominent content area displayed when the user focuses on your app icon on the Apple TV home screen. It is prime real estate for showcasing content.

### Rules

**SHELF-01: Provide a Top Shelf extension.**
Apps should include a `TVTopShelfContentProvider` (tvOS 14+) that returns dynamic content. `TVTopShelfProvider` is deprecated since tvOS 14 — do not use it. A static Top Shelf is a missed opportunity for engagement.

**SHELF-02: Use the correct layout style for your content.**
- **Inset banner**: 1 large focused item with smaller items on either side. Best for featured or editorial content.
- **Sectioned content**: Multiple scrollable rows of items grouped by category. Best for media libraries.

**SHELF-03: Top Shelf items must deep-link into the app.**
Each item must open the corresponding content when selected. Never link all items to the same generic landing page.

**SHELF-04: Use high-quality, engaging imagery.**
Top Shelf images are displayed large on the home screen. Blurry, low-resolution, or text-heavy images look unprofessional. Recommended image sizes:
- Inset banner: 1940x624pt (@1x) or 3880x1248pt (@2x)
- Sectioned items: 404x608pt (@1x)

**SHELF-05: Keep Top Shelf content fresh.**
Update Top Shelf content regularly -- ideally reflecting recently added, trending, or personalized content. Stale Top Shelf content signals an abandoned app.

---

## 5. Media & Playback (MEDIUM)

Apple TV is primarily a media consumption device. Playback interfaces must follow established TV conventions.

### Rules

**MEDIA-01: Use standard transport controls.**
Provide play, pause, skip forward (10s), skip back (10s), and a scrubber timeline. Use `AVPlayerViewController` to get these for free with consistent behavior.

**MEDIA-02: Show an info overlay on swipe-down during playback.**
Swiping down during playback should reveal an info panel showing title, description, and metadata. Swiping down again or pressing Menu dismisses it.

**MEDIA-03: Support scrubbing via the touch surface.**
Swiping left/right on the touch surface during playback should scrub through the timeline. Show thumbnail previews during scrubbing when possible.

**MEDIA-04: Support subtitles and alternative audio tracks.**
Provide access to subtitle selection and audio track switching via the info overlay or the standard player controls.

**MEDIA-05: Support Picture in Picture where appropriate.**
For video content, allow PiP so users can browse other content while watching. Implement `AVPictureInPictureController` integration.

**MEDIA-06: Remember playback position.**
When a user returns to previously watched content, resume from where they left off. Display a progress indicator on content thumbnails.

**MEDIA-07: Handle interruptions gracefully.**
If the user presses the TV button or switches apps during playback, save position and pause cleanly. Resume without re-buffering when the user returns.

---

## 6. Tab Bar (MEDIUM)

The tvOS tab bar sits at the top of the screen, unlike iOS where it sits at the bottom. It provides primary navigation between app sections.

### Rules

**TAB-01: Place the tab bar at the top of the screen.**
This is the standard tvOS convention. Bottom tab bars are an iOS pattern and feel wrong on TV.

**TAB-02: Tab bar should be translucent and overlay content.**
The tab bar floats over content with a blur effect. When the user focuses on the tab bar, content shifts down to make room.

**TAB-03: Use 3-7 tabs.**
Fewer than 3 tabs suggests the app is too simple for tab navigation. More than 7 tabs becomes difficult to navigate with horizontal swiping.

**TAB-04: Every tab must have a text label.**
Icon-only tabs are insufficient at TV viewing distances. Text labels are required for clarity. Icons may accompany text but are not required.

**TAB-05: Focus on the tab bar should feel lightweight.**
When focus moves to the tab bar, it should appear smoothly. Content preview should be visible beneath the translucent bar. Switching tabs should update the content below immediately or show a loading state.

**TAB-06: Remember the selected tab across app launches.**
If the user was on the "Search" tab when they left the app, return to "Search" when they re-open it.

---

## 7. Accessibility (CRITICAL)

Apple TV supports VoiceOver. Sighted users use focus navigation; VoiceOver users additionally hear spoken descriptions. Both must work.

### Rules

**ACCESS-01: Every interactive element must have a meaningful accessibility label.**
Icon-only buttons and image cards must have labels. The focused item's name is announced by VoiceOver when focus arrives.

**ACCESS-02: Provide accessibility hints for non-obvious interactions.**
If tapping a card does something other than opening the content (e.g., launching a trailer rather than full playback), describe this with an accessibility hint.

**ACCESS-03: Ensure VoiceOver focus order matches visual focus order.**
VoiceOver must traverse elements in the same order that focus engine navigation produces. Custom focus ordering via `UIFocusGuide` must not create discontinuities in the VoiceOver reading order.

**ACCESS-04: Respect Reduce Motion.**
Parallax effects and other animations must be reduced or disabled when the user enables Reduce Motion in Accessibility settings.

**ACCESS-05: Respond to Bold Text.**
When the user enables Bold Text, custom-rendered text must adapt. SwiftUI dynamic type styles handle this automatically; custom text rendering must check `UIAccessibility.isBoldTextEnabled` or use `@Environment(\.legibilityWeight)`.

**ACCESS-06: Respond to Increase Contrast.**
When the user enables Increase Contrast (Darker System Colors), custom colors must provide higher-contrast variants. Use `@Environment(\.colorSchemeContrast)` in SwiftUI or `UIAccessibility.isDarkerSystemColorsEnabled` in UIKit to detect and apply appropriate values.

**ACCESS-07: Respect Dynamic Type / Larger Text.**
tvOS supports the "Larger Text" accessibility setting via `UIContentSizeCategory`. Use SwiftUI semantic text styles (`Font.TextStyle`) so text scales automatically. For UIKit, scale custom fonts with `UIFontMetrics` relative to a base `UIFont.TextStyle`.

**Correct:**
```swift
// SwiftUI — semantic text styles scale with Larger Text automatically
Text("Now Playing")
    .font(.title2)        // Scales with UIContentSizeCategory
Text("Episode description")
    .font(.body)          // Scales with UIContentSizeCategory

// UIKit — scale custom font with UIFontMetrics
let baseFont = UIFont(name: "CustomFont-Regular", size: 29)!
let scaledFont = UIFontMetrics(forTextStyle: .body).scaledFont(for: baseFont)
label.font = scaledFont
label.adjustsFontForContentSizeCategory = true
```

**Incorrect:**
```swift
// SwiftUI — hardcoded size ignores Larger Text preference
Text("Now Playing")
    .font(.system(size: 29)) // Does not scale

// UIKit — hardcoded font ignores UIContentSizeCategory
label.font = UIFont(name: "CustomFont-Regular", size: 29)
// Missing adjustsFontForContentSizeCategory = true
```

---

## Evaluation Checklist

Use this checklist when reviewing a tvOS app design or implementation.

### Focus System
- [ ] Every interactive element has a visible, distinct focus state
- [ ] Focus movement is predictable in all directions
- [ ] No focus traps exist anywhere in the app
- [ ] Focus guides bridge layout gaps
- [ ] Parallax effect is applied to content cards and icons
- [ ] Default focus is set on every screen
- [ ] Focus memory is preserved when navigating back

### Siri Remote
- [ ] Menu button navigates back on every screen
- [ ] Play/Pause controls media playback globally
- [ ] No complex gestures are required
- [ ] Click feedback is immediate and visible
- [ ] Siri/dictation supported for text input

### 10-Foot UI
- [ ] Body text is 29pt or larger
- [ ] High contrast ratios on all text
- [ ] Text content is concise, not paragraph-heavy
- [ ] Imagery is high resolution and visually clear
- [ ] Layout uses generous spacing with TV-safe margins
- [ ] No thin fonts or hairline strokes

### Top Shelf
- [ ] Top Shelf extension provides dynamic content
- [ ] All Top Shelf items deep-link correctly
- [ ] Images are high quality and correctly sized
- [ ] Content updates regularly

### Media & Playback
- [ ] Standard transport controls are available
- [ ] Scrubbing works via touch surface
- [ ] Subtitles and audio tracks are accessible
- [ ] Playback position is remembered
- [ ] Interruptions are handled gracefully

### Tab Bar
- [ ] Tab bar is at the top of the screen
- [ ] Tabs have text labels
- [ ] 3-7 tabs are used
- [ ] Selected tab persists across launches

### Accessibility
- [ ] Every interactive element and content card has a meaningful accessibility label
- [ ] Non-obvious interactions have accessibility hints
- [ ] VoiceOver focus order matches the visual focus engine order
- [ ] Parallax effects and decorative animations are disabled when Reduce Motion is enabled
- [ ] Bold Text preference is respected (SwiftUI handles automatically; custom text checks `isBoldTextEnabled`)
- [ ] Increase Contrast preference is respected (custom colors provide higher-contrast variants)
- [ ] Larger Text (Dynamic Type) preference is respected (use `Font.TextStyle` in SwiftUI or `UIFontMetrics` in UIKit)

---

## Anti-Patterns for TV

**Do not** bring mobile patterns directly to tvOS. The following are common mistakes:

| Anti-Pattern | Why It Fails | Correct Approach |
|-------------|-------------|-----------------|
| Bottom tab bar | iOS convention; feels wrong on TV | Use top tab bar |
| Small touch targets | Cannot precisely target with swipe navigation | Minimum 250x150pt cards |
| Dense text screens | Unreadable at 10-foot distance | Headlines + short descriptions only |
| Hamburger menus | No tap-to-reveal interaction on TV | Use tab bar or focus-driven menus |
| Pull-to-refresh | No pull gesture on Siri Remote | Auto-refresh or explicit refresh button |
| Toast notifications | Easy to miss on a large TV screen | Use modal alerts or persistent banners |
| Scroll indicators | Thin scrollbars invisible at distance | Use content peek (partially visible next item) |
| Pinch-to-zoom | Multi-finger gestures impossible on Siri Remote | Provide explicit zoom controls |
| Long forms | Keyboard input is painful on tvOS | Pre-fill, use profiles, or offload to iPhone |
| Thin/light typography | Disappears on TV displays | Medium weight minimum |
