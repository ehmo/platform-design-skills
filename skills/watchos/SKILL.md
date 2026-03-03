---
name: watchos-design-guidelines
description: Apple Human Interface Guidelines for Apple Watch. Use when building watchOS apps, complications, or workout features. Triggers on tasks involving Watch UI, Digital Crown, glanceable interfaces, or wrist-based interactions.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# watchOS Design Guidelines

Apple Watch is a personal, glanceable device worn on the wrist. Interactions are measured in seconds, not minutes. Every design decision must prioritize speed of comprehension and brevity of interaction.

---

## 1. Glanceable Design (CRITICAL)

The defining constraint of watchOS. If a user cannot extract the key information within 2 seconds of raising their wrist, the design has failed.

### Rules

- **W-GL-01**: Primary information must be visible without scrolling. The first screen is the only guaranteed screen.
- **W-GL-02**: Target interaction sessions of 5 seconds or less. Design for raise-glance-lower.
- **W-GL-03**: Use large, high-contrast text. Minimum effective body text is 16pt (system font). Titles should be 18pt or larger.
- **W-GL-04**: Limit text to essential content. Truncate or abbreviate aggressively. Use SF Symbols instead of text labels where meaning is unambiguous.
- **W-GL-05**: Respect wrist-down time. When the wrist lowers, the app enters an inactive state. Do not assume continuous user attention.
- **W-GL-06**: Prioritize a single piece of information per screen. If showing multiple data points, establish clear visual hierarchy with size, weight, and color.

### Screen Dimensions Reference

| Device | Screen Width | Screen Height | Corner Radius |
|--------|-------------|---------------|---------------|
| 41mm (Series 9/10) | 176px | 215px | 36px |
| 45mm (Series 9/10) | 198px | 242px | 39px |
| 49mm (Ultra 2) | 205px | 251px | 40px |

### Anti-Patterns

- Walls of text requiring scroll to understand context
- Small, dense data tables
- Requiring multiple taps before showing useful information
- Replicating an iPhone screen layout on Watch

---

## 2. Digital Crown (HIGH)

The Digital Crown is the primary physical input for scrolling and precise value selection. It provides haptic feedback and should feel purposeful.

### Rules

- **W-DC-01**: Use the Digital Crown as the primary scroll mechanism for vertical content. Do not rely solely on swipe gestures for scrolling.
- **W-DC-02**: For value pickers (time, quantity, sliders), bind the Crown to precise adjustments with haptic detents at each discrete value.
- **W-DC-03**: Do not override or conflict with system Crown behaviors. The system uses the Crown for volume control during media playback, scrolling in system UI, and Time Travel in complications.
- **W-DC-04**: Provide visual feedback synchronized with Crown rotation. The UI must respond frame-by-frame to Crown input with no perceptible lag.

### Anti-Patterns

- Ignoring the Crown and forcing all interaction through touch
- Custom Crown behaviors that conflict with system expectations
- Missing haptic feedback on discrete value changes
- Laggy or batched responses to Crown rotation

---

## 3. Navigation (HIGH)

Watch navigation must be shallow and predictable. Users should never feel lost or unable to return to a known state.

### Rules

- **W-NV-01**: Use vertical page scrolling as the default content navigation pattern. Pages scroll top-to-bottom with the Digital Crown.
- **W-NV-02**: Use `TabView` for top-level sections (max 5 tabs). Swipe horizontally between tabs. Each tab is a distinct functional area.
- **W-NV-03**: Use `NavigationStack` for hierarchical drill-down. Limit hierarchy to 2-3 levels maximum. Every pushed view must have a back button (provided automatically by the system).
- **W-NV-04**: Avoid modal sheets for primary flows. Modals should be reserved for focused, single-purpose tasks (e.g., confirmation, quick input).
- **W-NV-05**: The app's most important action should be reachable within 1 tap from launch. Do not bury primary functionality behind menus or navigation.

### Navigation Pattern Reference

| Pattern | Use Case | Gesture |
|---------|----------|---------|
| Vertical scroll | Long-form content within a single view | Digital Crown / swipe up-down |
| TabView (horizontal pages) | Top-level app sections | Swipe left-right |
| NavigationStack (push/pop) | Hierarchical drill-down | Tap to push, swipe right or back button to pop |
| Modal sheet | Confirmation, focused input | Presented programmatically, dismiss via button or swipe down |

### Anti-Patterns

- Deep navigation hierarchies (4+ levels)
- Hamburger menus or hidden navigation drawers
- Tab bars with more than 5 items
- Forcing users to scroll through long lists to find key actions

---

## 4. Complications (HIGH)

Complications are the most visible surface of a Watch app. They live on the watch face and provide at-a-glance data without launching the app.

### Rules

- **W-CP-01**: Support multiple complication families to maximize watch face compatibility. At minimum support `circularSmall`, `graphicCorner`, and `graphicRectangular`.
- **W-CP-02**: Provide both tinted (single-color) and full-color variants. Tinted complications must remain legible when the system applies a single tint color.
- **W-CP-03**: Update complications via `TimelineProvider`. Provide future timeline entries when data is predictable (e.g., next calendar event, weather forecast). Keep data fresh -- stale complications erode trust.
- **W-CP-04**: Complication content must be meaningful without context. A user glancing at their watch face should immediately understand the data (e.g., "72F" not "72").
- **W-CP-05**: Tapping a complication must launch the app to a relevant context, not just the app's root view.

### Complication Family Reference

| Family | Shape | Typical Content |
|--------|-------|-----------------|
| `circularSmall` | Small circle | Single value, icon, or gauge |
| `graphicCorner` | Curved, top corners | Gauge with label, or text with icon |
| `graphicCircular` | Larger circle | Gauge, icon with value, or stack |
| `graphicRectangular` | Wide rectangle | Multi-line text, chart, or detailed view |
| `graphicExtraLarge` | Full-width circle | Large gauge or prominent single value |

### Anti-Patterns

- Supporting only one complication family
- Stale data that does not update for hours
- Complication tap landing on generic app home instead of relevant content
- Illegible complications in tinted mode (insufficient contrast)

---

## 5. Always On Display (MEDIUM)

When the user's wrist is down, watchOS enters an Always On state showing a dimmed version of the current app. This must be handled intentionally.

### Rules

- **W-AO-01**: Reduce visual complexity in the Always On state. Remove animations, secondary UI elements, and non-essential detail. Keep only the most critical information visible.
- **W-AO-02**: Hide sensitive or private data (e.g., message content, health details, financial information) in the dimmed state. Use redacted or placeholder content.
- **W-AO-03**: Reduce update frequency in Always On. Update the display no more than once per minute. Use `TimelineView` with a `.everyMinute` schedule for time-sensitive content.
- **W-AO-04**: Use the system-provided dimming behaviors. Do not implement custom dimming. The system automatically reduces brightness and can apply a tint. Ensure your content remains legible at reduced brightness.
- **W-AO-05**: Test both active and Always On states. The transition between states must feel seamless -- layout should not shift or jump when the wrist raises.

### Anti-Patterns

- Showing identical UI in active and Always On states (wastes battery, may expose private data)
- Animations or frequent updates in Always On state
- Layout shifts when transitioning between active and dimmed states
- Forgetting to redact sensitive information

---

## 6. Workouts & Health (MEDIUM)

Workout and health apps have unique requirements: extended sessions, live metrics, and body-awareness features.

### Rules

- **W-WK-01**: Display live workout metrics in large, high-contrast text. Heart rate, duration, distance, and calories should be readable mid-exercise without stopping.
- **W-WK-02**: Use haptic feedback for milestones (lap completed, goal reached, heart rate zone change). Haptics are essential because users may not be looking at the screen during exercise.
- **W-WK-03**: Support auto-pause detection for relevant workout types (running, walking). Users expect the workout to pause when they stop moving and resume when they start again.
- **W-WK-04**: Enable WaterLock during swimming workouts. This disables the touchscreen to prevent water interaction. The Digital Crown is used to eject water and unlock.
- **W-WK-05**: Show a clear summary screen at workout completion with key metrics. Allow the user to save or discard the workout with a single action.

### Anti-Patterns

- Small metric text that requires squinting or stopping to read
- Missing haptic feedback for important workout events
- No auto-pause support for outdoor workouts
- Requiring complex interaction to end or save a workout

---

## 7. Notifications (MEDIUM)

Watch notifications must be brief and actionable. The user's wrist is raised for only a moment.

### Rules

- **W-NT-01**: Design Short Look notifications with only a title, app icon, and app name. This is what the user sees on initial wrist raise. It must communicate the notification's purpose instantly.
- **W-NT-02**: Design Long Look notifications with full content and up to 4 action buttons. The user reaches Long Look by continuing to look at the notification. Include the most useful actions inline.
- **W-NT-03**: Use appropriate haptic notification types. Match the urgency: `.notification` for standard alerts, `.directionUp` for positive events, `.directionDown` for negative events, `.success`/`.failure`/`.retry` for outcomes.
- **W-NT-04**: Do not over-notify. Excessive notifications cause users to disable them entirely. Batch non-urgent updates. Reserve Watch notifications for time-sensitive or actionable information.

### Haptic Type Reference

| Haptic | Use Case |
|--------|----------|
| `.notification` | General alerts |
| `.directionUp` | Positive event (goal reached, stock up) |
| `.directionDown` | Negative event (stock down, weather warning) |
| `.success` | Action completed successfully |
| `.failure` | Action failed |
| `.retry` | Try again prompt |
| `.start` | Activity beginning |
| `.stop` | Activity ending |
| `.click` | Discrete selection (Crown detent, picker) |

### Anti-Patterns

- Sending every iPhone notification to the Watch
- Notifications without actionable buttons (forcing app launch)
- Using the same haptic type for all notifications regardless of content
- Long notification text that requires extensive scrolling

---

## Evaluation Checklist

Use this checklist when reviewing a watchOS design or implementation.

### Glanceability
- [ ] Can the user understand the primary content within 2 seconds?
- [ ] Is the most important information visible without scrolling?
- [ ] Is body text at least 16pt with sufficient contrast?
- [ ] Are interactions completable in under 5 seconds?

### Digital Crown
- [ ] Does the Crown scroll vertical content?
- [ ] Do value pickers provide haptic detents?
- [ ] Are there no conflicts with system Crown behaviors?

### Navigation
- [ ] Is the primary action reachable within 1 tap from launch?
- [ ] Is the navigation hierarchy 3 levels or fewer?
- [ ] Does every pushed view have a back button?
- [ ] Are top-level sections organized in a TabView (if applicable)?

### Complications
- [ ] Are multiple complication families supported?
- [ ] Do complications work in both tinted and full-color modes?
- [ ] Is complication data updated via TimelineProvider?
- [ ] Does tapping a complication open relevant context?

### Always On
- [ ] Is sensitive data hidden in the dimmed state?
- [ ] Is visual complexity reduced when inactive?
- [ ] Is the update frequency limited to once per minute or less?
- [ ] Is the transition between active and dimmed seamless (no layout shift)?

### Workouts
- [ ] Are live metrics displayed in large, high-contrast text?
- [ ] Are haptics used for milestones?
- [ ] Is auto-pause supported for applicable workout types?
- [ ] Is the workout summary accessible with a single action?

### Notifications
- [ ] Is the Short Look meaningful (title + icon)?
- [ ] Does the Long Look include inline actions?
- [ ] Are haptic types matched to notification urgency?
- [ ] Is notification frequency appropriate (not excessive)?
