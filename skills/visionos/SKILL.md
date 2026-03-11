---
name: visionos-design-guidelines
description: Apply Apple HIG to visionOS apps — design spatial windows with glass materials, implement gaze-based look-and-pinch navigation, create immersive volumes with RealityView, configure hand gesture interactions, and build ornament-based toolbars/tab bars. Triggers on tasks involving visionOS, RealityKit, spatial UI, or mixed reality.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# visionOS Design Guidelines

---

## 1. Spatial Layout [CRITICAL]

### Rules

**SL-01: Center content in the field of view.**
Place primary windows directly ahead at eye level. Comfortable vertical range: 30 degrees above/below eye level.

**SL-02: Maintain comfortable distance.**
Position windows at 1-2 meters from the user. System default: ~1.5 meters. Respect this unless there is a strong reason to override.

**SL-03: Never place content behind the user.**
Users cannot see content behind them without physically turning around. All UI elements must appear within the forward-facing hemisphere. If content must surround the user, provide clear navigation to rotate or reposition.

**SL-04: Respect personal space.**
Do not place 3D objects or windows closer than arm's length (~0.5 meters) from the user's head. Exception: direct-touch interactions where objects are intentionally within reach.

**SL-05: Use Z-depth to establish hierarchy.**
Elements closer to the user appear more prominent and interactive. Push secondary or background content further back. Use subtle depth offsets (a few centimeters) between layered elements rather than dramatic separation that fragments the interface.

**SL-06: Manage multiple windows thoughtfully.**
When displaying multiple windows, arrange them in a gentle arc around the user rather than stacking or overlapping. Each window should be individually repositionable. Avoid spawning too many simultaneous windows that overwhelm the space.

**SL-07: Anchor content to the environment, not the head.**
Windows and objects should stay fixed in world space as the user moves their head. Head-locked content (content that follows head movement) causes discomfort and motion sickness. Only use head-relative positioning for brief, transient elements like tooltips.

---

## 2. Eye & Hand Input [CRITICAL]

### Rules

**EH-01: Design for look-and-pinch as the primary interaction.**
User looks at an element (eyes target), then pinches thumb and index finger (hand confirms). Design all primary interactions around this model. Users do not need to raise hands or point at objects.

**EH-02: Minimum interactive target size is 60pt.**
All tappable elements must be at least 60 points in diameter. This is larger than iOS touch targets (44pt).

**EH-03: Provide hover feedback on gaze.**
When the user's eyes rest on an interactive element, show a visible highlight or subtle expansion to confirm targeting.

**EH-04: Support direct touch for close-range objects.**
When 3D objects or UI elements are within arm's reach, allow direct touch interaction (physically reaching out and tapping). Direct touch should feel natural: provide visual and audio feedback on contact. Use direct touch for immersive experiences where it enhances presence.

**EH-05: Never track gaze for content purposes.**
Do not use gaze direction to infer user interest, change content based on what the user looks at, or record where the user looks. The system does not expose raw eye-tracking data to apps.

**EH-06: Keep custom gestures simple and intuitive.**
If you define custom hand gestures beyond the system pinch, ensure they are easy to discover, physically comfortable to perform, and do not conflict with system gestures. Avoid gestures that require sustained hand raising, complex finger patterns, or two-handed coordination for basic actions.

**EH-07: Do not require precise hand positioning.**
Do not require users to hold hands in specific positions, reach to specific locations, or maintain sustained gestures. Design for hands resting naturally at sides or in lap.

### Hover Effect Example

```swift
// Add hover effect for gaze targeting feedback
Button("Action") { /* handle tap */ }
    .hoverEffect(.highlight)
    .frame(minWidth: 60, minHeight: 60) // Minimum 60pt target

// Custom hover effect on any view
RoundedRectangle(cornerRadius: 12)
    .frame(width: 200, height: 60)
    .hoverEffect(.automatic)
    .contentShape(.hoverEffect, RoundedRectangle(cornerRadius: 12))
```

See [REFERENCE.md](REFERENCE.md) for interaction tables, target sizes, and anti-patterns.

---

## 3. Windows [HIGH]

### Rules

**WN-01: Use glass material as the default window background.**
Do not replace glass with opaque solid colors unless you have a specific design reason (such as media playback).

```swift
// Glass material window — system default, no extra config needed
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello, visionOS")
                .font(.title)
        }
        .padding()
        .glassBackgroundEffect()  // Explicit glass background
    }
}
```

**WN-02: Maintain standard window controls.**
Windows include a system-provided window bar at the bottom for repositioning and a close button. Do not hide, override, or replace these controls. Users rely on consistent window management across all apps.

**WN-03: Make windows resizable when appropriate.**
If your content benefits from different sizes (documents, browsers, media), support window resizing. Use the system resize handle. Define reasonable minimum and maximum sizes. Adapt layout responsively as the window resizes.

**WN-04: Place the tab bar as a leading ornament (left side).**
Position the tab bar as a vertical ornament on the leading (left) edge, not at the bottom as on iOS. Use SF Symbols for tab icons.

```swift
// Tab bar as leading ornament
TabView {
    HomeView()
        .tabItem { Label("Home", systemImage: "house") }
    SettingsView()
        .tabItem { Label("Settings", systemImage: "gear") }
}
.tabViewStyle(.sidebarAdaptable)  // Leading ornament on visionOS
```

**WN-05: Place the toolbar as a bottom ornament.**
Primary action controls appear in a toolbar ornament anchored to the bottom edge of the window.

```swift
// Toolbar as bottom ornament
ContentView()
    .ornament(attachmentAnchor: .scene(.bottom)) {
        HStack(spacing: 20) {
            Button(action: { /* play */ }) {
                Label("Play", systemImage: "play.fill")
            }
            Button(action: { /* share */ }) {
                Label("Share", systemImage: "square.and.arrow.up")
            }
        }
        .padding()
        .glassBackgroundEffect()
    }
```

**WN-06: Windows float in space with no fixed screen.**
Design content that looks correct from slight angles and at varying distances. Do not assume a fixed viewport size or pixel-perfect positioning.

---

## 4. Volumes [HIGH]

### Rules

**VL-01: Contain 3D content within the volume bounds.**
All content must fit within the defined volume dimensions. Content that escapes the bounds will be clipped.

```swift
// Volume setup with RealityView
struct VolumeContent: View {
    var body: some View {
        RealityView { content in
            if let model = try? await ModelEntity(named: "toy_robot") {
                model.scale = SIMD3(repeating: 0.5)
                content.add(model)
            }
        }
    }
}

// In App.swift — declare the volume window group
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }

        WindowGroup(id: "model-viewer") {
            VolumeContent()
        }
        .windowStyle(.volumetric)
        .defaultSize(width: 0.5, height: 0.5, depth: 0.5, in: .meters)
    }
}
```

**VL-02: Design for viewing from all angles.**
Users can physically walk around a volume or reposition it. Ensure 3D content looks correct from all viewing angles, not just the front. Avoid flat facades that look like cardboard cutouts from the side.

**VL-03: Do not require a specific viewing position.**
The user may view the volume from above, below, or any side. Content must remain comprehensible regardless of viewing angle. If orientation matters, use visual cues (a base, a label) rather than forcing a specific position.

**VL-04: Scale content appropriately for the space.**
Volumes should be sized relative to their content and the user's environment. A molecular model might be small and held at arm's length. An architectural visualization might fill a table. Consider the context in which users will interact with the volume.

**VL-05: Use volumes for self-contained 3D experiences.**
Volumes are not windows with 3D objects inside them. Use volumes when the 3D content is the primary experience (examining a product model, viewing a 3D chart). Use windows for primarily 2D interfaces that may include some 3D elements.

---

## 5. Immersive Spaces [HIGH]

### Rules

**IS-01: Start in the Shared Space.**
Apps launch into the Shared Space by default. Only transition to more immersive experiences when the user explicitly requests it. Do not force immersion on launch.

**IS-02: Use progressive immersion.**
Move through levels gradually: Shared Space → Full Space (passthrough remains) → Full Immersion (completely virtual). Each step should be user-initiated.

```swift
// Immersive space transition
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()  // Starts in Shared Space
        }

        ImmersiveSpace(id: "immersive-view") {
            ImmersiveContent()
        }
        .immersionStyle(selection: .constant(.mixed), in: .mixed, .full)
    }
}

// Trigger transition from a button
struct ContentView: View {
    @Environment(\.openImmersiveSpace) var openImmersiveSpace
    @Environment(\.dismissImmersiveSpace) var dismissImmersiveSpace

    var body: some View {
        Button("Enter Immersive") {
            Task { await openImmersiveSpace(id: "immersive-view") }
        }
    }
}
```

**IS-03: Always provide an exit path.**
Users must always be able to return to a less immersive state or exit the experience entirely. The Digital Crown is the system-level escape. Within your app, provide clear controls to reduce immersion. Never trap users in an immersive experience.

**IS-04: Use passthrough for safety.**
In experiences where users might move physically, maintain passthrough of the real environment or provide a guardian boundary. Users need awareness of physical obstacles, other people, and room boundaries. Full immersion is only appropriate when the user is stationary.

**IS-05: Dim passthrough gradually.**
Dim the passthrough environment gradually rather than cutting to black. Use smooth, animated transitions between immersion levels.

**IS-06: Do not assume room layout or size.**
Users are in diverse physical spaces. Do not design experiences that require a minimum room size, assume a clear floor area, or expect specific furniture placement. Gracefully adapt to whatever physical space is available.

**IS-07: Provide spatial audio cues.**
In immersive spaces, use spatial audio to help users orient. Sounds should come from the direction of their source in the virtual environment. Audio cues can guide attention and provide feedback without requiring visual focus.

---

## 6. Materials & Depth [MEDIUM]

### Rules

**MD-01: Use the system glass material for UI surfaces.**
The glass material is the foundation of visionOS UI. It provides depth, translucency, and environmental integration. Use the system-provided glass variants (regular, thin, ultra-thin) rather than custom translucent materials.

**MD-02: Specular highlights respond to the environment.**
Materials in visionOS react to real-world lighting conditions. Design elements that leverage this: subtle specular highlights on interactive elements reinforce their dimensionality. Do not flatten materials with purely matte surfaces.

**MD-03: Layer materials to create depth hierarchy.**
Use lighter/thicker glass for foreground elements and darker/thinner glass for background. Sidebars use a slightly different glass tint than content areas. This layering creates natural visual hierarchy without sharp borders.

**MD-04: Apply vibrancy for text readability.**
Text over glass materials uses vibrancy effects to remain legible regardless of the background environment. Use the system text styles which include appropriate vibrancy. Custom text rendering over glass must account for varying background lightness and color.

**MD-05: Use shadows and highlights for elevation.**
Elements that float above the window surface (popovers, menus, hover states) should cast subtle shadows and show slight specular highlights on their upper edges. These depth cues help users understand the spatial relationship between interface layers.

**MD-06: Avoid fully opaque backgrounds in shared space.**
Opaque surfaces in the shared space create visual holes in the environment. Use translucent glass materials that let the environment show through. Exceptions include media playback (video, photos) where an opaque background improves the viewing experience.

---

## 7. Ornaments [MEDIUM]

### Rules

**OR-01: Attach controls as ornaments rather than inline.**
Toolbars, tab bars, and persistent action buttons belong as ornaments, not embedded within the window content area. Ornaments keep the content area clean and establish a clear spatial hierarchy between controls and content.

**OR-02: Place primary actions in the bottom ornament.**
The bottom edge ornament is the primary location for action controls (play/pause, formatting tools, share). This position is ergonomically accessible and visually prominent without obscuring content.

**OR-03: Place navigation in the leading (left) ornament.**
App-level navigation (tab bar equivalent) attaches to the leading edge of the window. This keeps navigation persistent and accessible while leaving the content area and bottom ornament for contextual controls.

**OR-04: Do not occlude window content with ornaments.**
Ornaments extend outward from the window edge, not inward. They should not cover or overlap the window's content area. Size ornaments appropriately so they remain functional without becoming visually dominant over the content.

**OR-05: Show ornaments contextually when appropriate.**
Not all ornaments need to be visible at all times. Toolbars can appear on hover (when the user looks at the window) and fade when the user looks away. This keeps the interface clean while maintaining discoverability.

**OR-06: Use standard ornament styling.**
Ornaments use the same glass material system as windows but at a slightly different depth. Use system-provided ornament containers rather than custom floating UI. This ensures visual consistency with other visionOS apps.

---

## Implementation Workflow

1. **Set up window with glass material** — Create your `WindowGroup` with `.glassBackgroundEffect()`. Validate: window renders with translucent glass, not opaque.
2. **Configure navigation as leading ornament** — Use `.tabViewStyle(.sidebarAdaptable)` for the tab bar. Validate: tab bar appears on left edge of window.
3. **Configure toolbar as bottom ornament** — Attach action controls via `.ornament(attachmentAnchor: .scene(.bottom))`. Validate: toolbar floats below window, not inside content area.
4. **Add hover effects to all interactive elements** — Apply `.hoverEffect(.highlight)` and ensure minimum 60pt targets. Validate: every button/link highlights on gaze.
5. **Build volumes for 3D content** — Use `RealityView` inside a `.windowStyle(.volumetric)` scene. Validate: model renders within bounds, visible from all angles.
6. **Implement immersive space transitions** — Add `ImmersiveSpace` scene, trigger via `openImmersiveSpace`. Validate: transition is smooth, exit via Digital Crown works.
7. **Run Evaluation Checklist** — Walk through every item below before shipping.

---

## Evaluation Checklist

Use this checklist to evaluate a visionOS design or implementation.

### Spatial Layout
- [ ] Primary content centered in forward field of view
- [ ] Content placed at comfortable distance (1-2m for windows)
- [ ] No content placed behind the user
- [ ] Personal space respected (nothing closer than ~0.5m)
- [ ] Z-depth used meaningfully for hierarchy
- [ ] Multiple windows arranged in arc, not stacked
- [ ] Content anchored to world space, not head-locked

### Eye & Hand Input
- [ ] All interactions work with look-and-pinch
- [ ] All interactive targets >= 60pt
- [ ] Hover states visible on all interactive elements
- [ ] Direct touch supported for close-range objects
- [ ] No gaze tracking for content or analytics purposes
- [ ] Custom gestures are simple and discoverable
- [ ] No sustained hand-raising required

### Windows
- [ ] Glass material used as default background
- [ ] Standard window bar and close button present
- [ ] Tab bar positioned as leading ornament
- [ ] Toolbar positioned as bottom ornament
- [ ] Layout adapts to different window sizes
- [ ] Content designed for floating in space

### Volumes
- [ ] Content contained within volume bounds
- [ ] Content looks correct from all viewing angles
- [ ] No specific viewing position required
- [ ] Scale appropriate for content and context

### Immersive Spaces
- [ ] App starts in Shared Space
- [ ] Immersion increases progressively
- [ ] Clear exit path always available
- [ ] Passthrough maintained where safety requires it
- [ ] Transitions between immersion levels are smooth
- [ ] No assumptions about room size or layout

### Materials & Depth
- [ ] System glass material used for UI surfaces
- [ ] Material layering creates depth hierarchy
- [ ] Text uses vibrancy for readability over glass
- [ ] Shadows and highlights indicate elevation
- [ ] No fully opaque surfaces in shared space (except media)

### Ornaments
- [ ] Controls attached as ornaments, not inline
- [ ] Primary actions in bottom ornament
- [ ] Navigation in leading ornament
- [ ] Ornaments extend outward, not over content
- [ ] Standard ornament styling used

