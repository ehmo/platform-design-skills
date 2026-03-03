---
name: visionos-design-guidelines
description: Apple Human Interface Guidelines for Apple Vision Pro. Use when building spatial computing apps, implementing eye/hand input, or designing immersive experiences. Triggers on tasks involving visionOS, RealityKit, spatial UI, or mixed reality.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# visionOS Design Guidelines

Comprehensive design rules for Apple Vision Pro based on Apple Human Interface Guidelines. These rules ensure spatial computing apps are comfortable, intuitive, and consistent with platform conventions.

---

## 1. Spatial Layout [CRITICAL]

Spatial layout determines user comfort and usability. Poor placement causes neck strain, disorientation, or inaccessible content.

### Rules

**SL-01: Center content in the field of view.**
Place primary windows and content directly ahead of the user at eye level. The comfortable vertical viewing range is approximately 30 degrees above and below eye level. Content outside this range requires head movement and causes fatigue.

**SL-02: Maintain comfortable distance.**
Position content at a natural distance from the user, typically 1-2 meters for windows. Content too close feels invasive; content too far is hard to read. The system default placement is approximately 1.5 meters. Respect this unless there is a strong reason to override.

**SL-03: Never place content behind the user.**
Users cannot see content behind them without physically turning around. All UI elements must appear within the forward-facing hemisphere. If content must surround the user, provide clear navigation to rotate or reposition.

**SL-04: Respect personal space.**
Do not place 3D objects or windows closer than arm's length (~0.5 meters) from the user's head. Objects inside personal space cause discomfort and a feeling of intrusion. Direct-touch interactions are the exception, where objects are intentionally within reach.

**SL-05: Use Z-depth to establish hierarchy.**
Elements closer to the user appear more prominent and interactive. Push secondary or background content further back. Use subtle depth offsets (a few centimeters) between layered elements rather than dramatic separation that fragments the interface.

**SL-06: Manage multiple windows thoughtfully.**
When displaying multiple windows, arrange them in a gentle arc around the user rather than stacking or overlapping. Each window should be individually repositionable. Avoid spawning too many simultaneous windows that overwhelm the space.

**SL-07: Anchor content to the environment, not the head.**
Windows and objects should stay fixed in world space as the user moves their head. Head-locked content (content that follows head movement) causes discomfort and motion sickness. Only use head-relative positioning for brief, transient elements like tooltips.

---

## 2. Eye & Hand Input [CRITICAL]

visionOS uses indirect interaction as its primary input model: users look at a target and pinch to select. This is fundamentally different from touch or mouse input.

### Rules

**EH-01: Design for look-and-pinch as the primary interaction.**
The standard interaction is: user looks at an element (eyes provide targeting), then pinches thumb and index finger together (hand provides confirmation). Design all primary interactions around this model. Users do not need to raise their hands or point at objects.

**EH-02: Minimum interactive target size is 60pt.**
Eye tracking has inherent imprecision. All tappable elements must be at least 60 points in diameter to be reliably targeted by gaze. This is larger than iOS touch targets (44pt). Smaller targets cause frustration and mis-selections.

**EH-03: Provide hover feedback on gaze.**
When the user's eyes rest on an interactive element, show a visible highlight or subtle expansion to confirm the element is targeted. This feedback is essential because there is no cursor. Without hover states, users cannot tell what they are about to select.

**EH-04: Support direct touch for close-range objects.**
When 3D objects or UI elements are within arm's reach, allow direct touch interaction (physically reaching out and tapping). Direct touch should feel natural: provide visual and audio feedback on contact. Use direct touch for immersive experiences where it enhances presence.

**EH-05: Never track gaze for content purposes.**
Eye position is used exclusively for system interaction targeting. Do not use gaze direction to infer user interest, change content based on what the user looks at, or record where the user looks. This is a core privacy principle of the platform. The system does not expose raw eye-tracking data to apps.

**EH-06: Keep custom gestures simple and intuitive.**
If you define custom hand gestures beyond the system pinch, ensure they are easy to discover, physically comfortable to perform, and do not conflict with system gestures. Avoid gestures that require sustained hand raising, complex finger patterns, or two-handed coordination for basic actions.

**EH-07: Do not require precise hand positioning.**
Users interact with hands resting naturally in their lap or at their sides. Do not require users to hold their hands in specific positions, reach to specific locations, or maintain sustained gestures. The indirect interaction model exists specifically to reduce physical effort.

### Spatial Interaction Quick Reference

| Interaction | Method | Use Case |
|---|---|---|
| Tap / Select | Look + pinch | Buttons, links, list items |
| Scroll | Look + pinch-and-drag | Lists, long content |
| Zoom | Two-hand pinch | Maps, images, 3D models |
| Rotate | Two-hand twist | 3D object manipulation |
| Drag | Look + pinch-hold-and-move | Repositioning windows |
| Direct touch | Reach and tap | Close-range 3D objects |
| Long press | Look + pinch-and-hold | Context menus |

### Target Size Reference

| Element | Minimum Size | Recommended Size |
|---|---|---|
| Buttons | 60pt | 60-80pt |
| List rows | 60pt height | 80pt height |
| Tab bar items | 60pt | 72pt |
| Close/dismiss | 60pt | 60pt |
| Toolbar items | 60pt | 60pt |
| 3D interactive objects | 60pt equivalent | Scale to context |

---

## 3. Windows [HIGH]

Windows in visionOS float in the user's physical space. They use a glass material that blends with the real-world environment.

### Rules

**WN-01: Use glass material as the default window background.**
The system glass material dynamically adapts to the user's real-world surroundings, providing a consistent and readable backdrop. Do not replace glass with opaque solid colors unless you have a specific design reason (such as media playback). Glass grounds the interface in the shared space.

**WN-02: Maintain standard window controls.**
Windows include a system-provided window bar at the bottom for repositioning and a close button. Do not hide, override, or replace these controls. Users rely on consistent window management across all apps.

**WN-03: Make windows resizable when appropriate.**
If your content benefits from different sizes (documents, browsers, media), support window resizing. Use the system resize handle. Define reasonable minimum and maximum sizes. Adapt layout responsively as the window resizes.

**WN-04: Place the tab bar as a leading ornament (left side).**
On visionOS, the tab bar (app navigation) is positioned as a vertical ornament on the leading (left) edge of the window, not at the bottom as on iOS. This keeps navigation accessible without consuming window content area. Use SF Symbols for tab icons.

**WN-05: Place the toolbar as a bottom ornament.**
Primary action controls appear in a toolbar ornament anchored to the bottom edge of the window. This positions controls near the user's natural hand resting position and keeps the content area unobstructed.

**WN-06: Windows float in space with no fixed screen.**
There is no fixed display. Windows exist in the user's physical environment. Design content that looks correct when viewed from slight angles and at varying distances. Avoid designs that assume a fixed viewport size or pixel-perfect positioning.

---

## 4. Volumes [HIGH]

Volumes display 3D content within a bounded box. They are ideal for 3D models, visualizations, and objects users can examine from multiple angles.

### Rules

**VL-01: Contain 3D content within the volume bounds.**
All content must fit within the defined volume dimensions. Content that escapes the bounds will be clipped. Size the volume appropriately for the content it holds and respect the system-enforced limits.

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

visionOS supports a spectrum of immersion from shared space (apps alongside reality) to full immersion (complete virtual environment).

### Rules

**IS-01: Start in the Shared Space.**
Apps launch into the Shared Space by default, where multiple app windows coexist alongside the real world. Only transition to more immersive experiences when the user explicitly requests it. Do not force immersion on launch.

**IS-02: Use progressive immersion.**
Move through immersion levels gradually: Shared Space (windows alongside reality) to Full Space (your app takes over but passthrough remains) to Full Immersion (completely virtual environment). Each step should feel intentional and user-initiated.

**IS-03: Always provide an exit path.**
Users must always be able to return to a less immersive state or exit the experience entirely. The Digital Crown is the system-level escape. Within your app, provide clear controls to reduce immersion. Never trap users in an immersive experience.

**IS-04: Use passthrough for safety.**
In experiences where users might move physically, maintain passthrough of the real environment or provide a guardian boundary. Users need awareness of physical obstacles, other people, and room boundaries. Full immersion is only appropriate when the user is stationary.

**IS-05: Dim passthrough gradually.**
When transitioning to increased immersion, dim the passthrough environment gradually rather than cutting to black. Abrupt visual changes are disorienting. Use smooth, animated transitions between immersion levels.

**IS-06: Do not assume room layout or size.**
Users are in diverse physical spaces. Do not design experiences that require a minimum room size, assume a clear floor area, or expect specific furniture placement. Gracefully adapt to whatever physical space is available.

**IS-07: Provide spatial audio cues.**
In immersive spaces, use spatial audio to help users orient. Sounds should come from the direction of their source in the virtual environment. Audio cues can guide attention and provide feedback without requiring visual focus.

---

## 6. Materials & Depth [MEDIUM]

visionOS uses a physically-based material system that responds to real-world lighting. Proper use of materials creates depth hierarchy and ensures readability.

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

Ornaments are UI controls that attach to the edges of windows, floating partially outside the window bounds. They provide toolbars, navigation, and contextual actions.

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

---

## Anti-Patterns

These are common mistakes in visionOS design. Avoid them.

| Anti-Pattern | Problem | Correct Approach |
|---|---|---|
| Head-locked UI | Causes motion sickness, feels claustrophobic | Anchor UI to world space |
| Tiny tap targets | Eye tracking cannot reliably target < 60pt | Minimum 60pt interactive targets |
| No hover states | Users cannot tell what is interactive | Always show highlight on gaze |
| Opaque windows in shared space | Creates visual holes in environment | Use system glass material |
| Forced full immersion | Disorienting, traps users | Start in shared space, progressive immersion |
| Content behind user | Invisible, requires full body rotation | Keep content in forward hemisphere |
| Gaze-driven content | Privacy violation, feels surveilled | Use gaze only for system targeting |
| Flat 3D volumes | Looks like cardboard cutout from side | Design for all viewing angles |
| Inline toolbars | Wastes content space, breaks conventions | Use ornaments for controls |
| Small room assumptions | Fails in tight spaces | Adapt to available physical space |
| Abrupt immersion changes | Disorienting, breaks presence | Gradual transitions with animation |
| Sustained arm raising | Physical fatigue in minutes | Design for hands resting at sides |
| Custom window chrome | Breaks platform consistency | Use system window controls |
| Z-fighting layers | Visual flicker, unclear hierarchy | Use intentional depth offsets |
