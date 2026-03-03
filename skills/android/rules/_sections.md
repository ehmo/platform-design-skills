# Android Design Guidelines — Section Index

Quick reference for locating rules by category and ID.

## Section Map

| # | Section | Priority | Rules | Topics |
|---|---------|----------|-------|--------|
| 1 | Material You & Theming | CRITICAL | R1.1–R1.11 | Dynamic color, color roles, light/dark themes, custom seeds, tonal palettes |
| 2 | Navigation | CRITICAL | R2.1–R2.12 | Navigation bar, navigation rail, navigation drawer, predictive back, up vs back |
| 3 | Layout & Responsive | HIGH | R3.1–R3.10 | Window size classes, Material grid, edge-to-edge, insets, foldable support |
| 4 | Typography | HIGH | R4.1–R4.5 | Type scale, sp units, font scaling, line height, custom fonts |
| 5 | Components | HIGH | R5.1–R5.18 | FAB, top app bar, bottom sheets, dialogs, snackbars, chips, cards |
| 6 | Accessibility | CRITICAL | R6.1–R6.12 | TalkBack, contentDescription, touch targets, contrast, focus order |
| 7 | Gestures & Input | MEDIUM | R7.1–R7.7 | System gestures, pull to refresh, swipe to dismiss, long press, ripple |
| 8 | Notifications | MEDIUM | R8.1–R8.9 | Channels, importance, messaging style, expandable, foreground service |
| 9 | Permissions & Privacy | HIGH | R9.1–R9.8 | Runtime permissions, rationale, photo picker, approximate location |
| 10 | System Integration | MEDIUM | R10.1–R10.10 | Widgets (Glance), app shortcuts, deep links, share sheet |

## Rule Index

### 1. Material You & Theming [CRITICAL]

- **R1.1** — Provide fallback static color scheme for devices below Android 12
- **R1.2** — Never hardcode color hex values; use theme color roles
- **R1.3** — Test with at least 3 wallpapers for dynamic color harmony
- **R1.4** — Foreground elements must use matching `on` color role
- **R1.5** — Use `surface` variants for backgrounds, not `primary`/`secondary`
- **R1.6** — Use `tertiary` sparingly for accent only
- **R1.7** — Always support both light and dark themes
- **R1.8** — Dark theme uses tonal mapping, not pure black
- **R1.9** — Provide manual theme override (System/Light/Dark)
- **R1.10** — Generate tonal palettes from seed via Material Theme Builder
- **R1.11** — Support dynamic color as default; custom colors as fallback

### 2. Navigation [CRITICAL]

- **R2.1** — Navigation Bar for 3-5 destinations on compact screens
- **R2.2** — Always show labels on navigation bar items
- **R2.3** — Filled icons for selected, outlined for unselected
- **R2.4** — Active indicator uses `secondaryContainer`
- **R2.5** — Navigation Rail on medium (600dp+) and expanded screens
- **R2.6** — Optionally include FAB in rail header
- **R2.7** — Rail labels optional but recommended
- **R2.8** — Modal drawer on compact, permanent drawer on expanded
- **R2.9** — Group drawer items with dividers and section headers
- **R2.10** — Opt in to predictive back; use `OnBackInvokedCallback`
- **R2.11** — System back != Up button; they may navigate differently
- **R2.12** — No "are you sure?" on back unless unsaved user input

### 3. Layout & Responsive [HIGH]

- **R3.1** — Use `WindowSizeClass` for responsive decisions
- **R3.2** — Never use fixed pixel breakpoints
- **R3.3** — Support all three width size classes
- **R3.4** — Max content width ~840dp on expanded screens
- **R3.5** — Consistent horizontal margins per grid spec
- **R3.6** — Call `enableEdgeToEdge()` before `setContent`
- **R3.7** — Use `WindowInsets` to pad content from system bars
- **R3.8** — Scrollable content scrolls behind transparent system bars
- **R3.9** — Detect fold/hinge; avoid content across fold
- **R3.10** — Use `ListDetailPaneScaffold` for foldable-aware layouts

### 4. Typography [HIGH]

- **R4.1** — Use `sp` units for all text sizes
- **R4.2** — Minimum 12sp body text, 11sp labels
- **R4.3** — Reference `MaterialTheme.typography` roles, not hardcoded sizes
- **R4.4** — Test at 200% font scale; no clipping
- **R4.5** — Line height 1.2-1.5x font size

### 5. Components [HIGH]

- **R5.1** — At most one FAB per screen
- **R5.2** — FAB at bottom-end, above Navigation Bar
- **R5.3** — FAB uses `primaryContainer` by default
- **R5.4** — Prefer `ExtendedFloatingActionButton` with label
- **R5.5** — Small top app bar for most screens; medium/large for prominent titles
- **R5.6** — Connect scroll behavior to top app bar
- **R5.7** — Limit action icons to 2-3; overflow the rest
- **R5.8** — Modal bottom sheets for supplementary; standard for persistent
- **R5.9** — Bottom sheets must have visible drag handle
- **R5.10** — Sheet content must be scrollable if it overflows
- **R5.11** — Dialogs for critical decisions only
- **R5.12** — Confirm button is text button; dismiss on left
- **R5.13** — Dialog titles are concise questions or statements
- **R5.14** — Snackbars for brief non-critical feedback
- **R5.15** — Snackbars above Navigation Bar, below FAB
- **R5.16** — Include undo action when operation is reversible
- **R5.17** — Correct chip type for use case (Filter/Assist/Input/Suggestion)
- **R5.18** — Chips in horizontal scroll or flow layout, not stacked vertically

### 6. Accessibility [CRITICAL]

- **R6.1** — Every interactive element needs `contentDescription`
- **R6.2** — Describe action/meaning, not visual appearance
- **R6.3** — `mergeDescendants` for grouped related elements
- **R6.4** — Provide `customActions` for swipe/long-press actions
- **R6.5** — Minimum 48x48dp touch targets
- **R6.6** — Do not reduce touch targets to save space
- **R6.7** — Text contrast >= 4.5:1 normal, >= 3:1 large
- **R6.8** — Never use color alone to convey information
- **R6.9** — Support bold text and high contrast settings
- **R6.10** — Logical focus order (top-to-bottom, start-to-end)
- **R6.11** — Move focus to logical target after navigation/dialog dismissal
- **R6.12** — Full operability via TalkBack, Switch Access, keyboard

### 7. Gestures & Input [MEDIUM]

- **R7.1** — No interactive elements in system gesture zones
- **R7.2** — Use `WindowInsets.systemGestures` to detect conflict zones
- **R7.3** — Swipe-to-dismiss must be undoable or confirmed
- **R7.4** — Non-gesture alternatives for all gesture actions
- **R7.5** — Material ripple on all tappable elements
- **R7.6** — Long press for context menus; never the only access path
- **R7.7** — Haptic feedback on long press

### 8. Notifications [MEDIUM]

- **R8.1** — Separate channels for each notification type
- **R8.2** — Choose importance levels conservatively
- **R8.3** — All notifications must have a tap action
- **R8.4** — Notification icons need `contentDescription`
- **R8.5** — `MessagingStyle` for conversations
- **R8.6** — Direct reply actions on message notifications
- **R8.7** — "Mark as read" action on message notifications
- **R8.8** — Use expandable styles for rich content
- **R8.9** — Foreground service notifications describe operation + stop action

### 9. Permissions & Privacy [HIGH]

- **R9.1** — Request permissions in context, not at launch
- **R9.2** — Show rationale before requesting
- **R9.3** — Graceful degradation on denial
- **R9.4** — Never request unnecessary permissions
- **R9.5** — Use Photo Picker instead of `READ_MEDIA_IMAGES`
- **R9.6** — Prefer `ACCESS_COARSE_LOCATION` unless precise is essential
- **R9.7** — One-time permissions for camera/mic in non-recording contexts
- **R9.8** — Privacy indicator when camera/mic actively in use

### 10. System Integration [MEDIUM]

- **R10.1** — Glance API for widgets with dynamic color
- **R10.2** — Widgets must work immediately after placement
- **R10.3** — Multiple widget sizes where practical
- **R10.4** — Rounded corners matching system widget shape
- **R10.5** — 2-4 static shortcuts; support dynamic shortcuts
- **R10.6** — Shortcut icons: simple silhouettes on circular background
- **R10.7** — Test shortcuts via long-press and Settings
- **R10.8** — Android App Links for public content URLs
- **R10.9** — Share sheet with rich previews
- **R10.10** — Handle incoming share intents with content type filtering

## Cross-References

| Topic | Primary Section | Also See |
|-------|----------------|----------|
| Dark theme | 1.3 | 6.3 (contrast) |
| Touch targets | 6.2 | 7.1 (gesture zones) |
| System bars | 3.3 | 7.1 (gesture insets) |
| FAB placement | 5.1 | 2.2 (rail header), 5.15 (snackbar) |
| Font scaling | 4.1 | 6.4 (accessibility) |
| Permissions | 9.1 | 9.5 (photo picker) |
| Navigation sizing | 2.5 (table) | 3.1 (window size classes) |
| Color roles | 1.2 | 2.4 (nav indicator), 5.3 (FAB color) |
