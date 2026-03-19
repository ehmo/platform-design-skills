# Changelog

## Unreleased

### Summary

Added a focused Human Processor Model pass across the skill pack as a supporting HCI lens, not a new top-level doctrine.

### Added

- Added HPM-informed guidance to the existing sections where it materially improves the rules:
  - iOS: recognition over recall in navigation; immediate waiting-state feedback in patterns
  - iPadOS: preserved visible navigation state; shortcut discoverability over memorization
  - macOS: stable menu command naming/location; lower-interruption feedback for routine actions
  - watchOS: tighter Digital Crown response guidance
  - tvOS: reduced re-orientation cost in focus navigation; lower-effort text-entry guidance
  - visionOS: immediate intent confirmation for eye/hand input; essential controls stay discoverable
  - Android: recognition over recall in navigation; explicit visible waiting states in components
  - Web: field-local instructions; prompt exposure of waiting states

### Documentation

- Updated `README.md` Sources to add the Human Processor Model references used for this pass
- Explicitly documented that HPM is a secondary reference and does not override Apple HIG, Material Design, or WCAG

### Notes

- No standalone HPM section was added to the repo
- Avoided turning HPM into hard numeric rules where the platform source is the real authority

## v1.1.0 — March 2026

### Summary

Comprehensive accuracy and coverage pass across all 8 platform skills. Fixes deprecated and fabricated API references, adds missing accessibility coverage, corrects WCAG citations, and brings rule counts and metadata up to date.

**Total changes:** 1,281 insertions across 33 files. Rule counts grew from ~300 to 450+ total rules.

---

### Bug fixes — incorrect or fabricated APIs

**iOS**
- Fixed `accessibilitySortPriority` code comments: higher values are read *first* by VoiceOver, not last
- Fixed `Rule 3.3`: removed inaccurate "`.bold` dynamic type variants" phrasing; added `@Environment(\.legibilityWeight)` SwiftUI path alongside UIKit `UIFontMetrics` path
- Fixed `Rule 9.6 LocationButton` code example: added missing `import CoreLocationUI` (caused compile error)
- Fixed `Rule 10.1`: interactive widgets with `Button`/`Toggle` + App Intents are supported since iOS 17 (rule incorrectly stated widgets are not interactive)

**iPadOS**
- Fixed `Rule 6.4` Pencil hover: replaced fabricated `override func pencilHoverChanged(_:)` UIKit method (does not exist) with correct `UIHoverGestureRecognizer` pattern
- Fixed `Rule 8.2`: replaced deprecated `UIScreen.didConnectNotification` (deprecated iOS 16) with scene-based `UIWindowScene` APIs
- Fixed `Rule 8.3`: replaced `UIScreen.bounds`/`UIScreen.scale` for external display queries (deprecated in multi-scene context) with `UIWindowScene.coordinateSpace.bounds` and `@Environment(\.displayScale)`
- Fixed screen size table: iPad is 11" (10th gen+), not 10.9"

**macOS**
- Fixed `Rule 1.3`: `CommandGroup(replacing: .toolbar)` was silently removing all built-in toolbar menu items; corrected to `CommandGroup(after: .toolbar)`
- Fixed keyboard quick reference: duplicate `Cmd+0` entry removed (had two conflicting actions assigned)
- Fixed `Rule 11.7`: removed fabricated SwiftUI environment key `\.accessibilityDisplayAdjustments` (does not exist); correct key is `\.colorSchemeContrast`
- Noted `Cmd+Ctrl+S` sidebar toggle is app-defined, not a HIG universal standard

**watchOS**
- Fixed `W-AC-05`/`W-AC-06`: replaced `UIAccessibility.isBoldTextEnabled` and `UIAccessibility.isDarkerSystemColorsEnabled` — UIKit is not available on watchOS; correct approach is `@Environment(\.legibilityWeight)` and `@Environment(\.colorSchemeContrast)`
- Fixed complication family names: replaced deprecated ClockKit names (`circularSmall`, `graphicCorner`, etc.) with current WidgetKit names (`accessoryCircular`, `accessoryCorner`, `accessoryRectangular`, `accessoryInline`)
- Fixed `metadata.json` reference: removed deprecated ClockKit documentation link; replaced with WidgetKit
- Fixed screen dimensions table: separated Series 9 (41mm/45mm) and Series 10 (42mm/46mm) which have different physical dimensions

**tvOS**
- Fixed `SHELF-01`: clarified that `TVTopShelfContentProvider` is current; `TVTopShelfProvider` was deprecated in tvOS 14
- Fixed `FOCUS-04`: replaced `TVMonogramView` parallax reference (it is a user-initials display component, unrelated to parallax) with correct LSR file and `didUpdateFocus` + `UIFocusAnimationCoordinator` approach; `UIMotionEffect` clarified as gyroscope micromotion only, not focus-driven animation
- Fixed parallax description: "device tilt" corrected to "Siri Remote motion" (Apple TV is stationary)
- Fixed `metadata.json`: removed deprecated TVUIKit documentation reference
- Fixed `ACCESS-07`: tvOS supports the "Larger Text" accessibility setting via `UIContentSizeCategory`

**visionOS**
- Fixed code example: `model3D` corrected to `Model3D` (capital M — actual SwiftUI/RealityKit component name)

**Android**
- Fixed `R2.10`/anti-patterns table/`AGENTS.md`: corrected back-navigation guidance — Compose apps use `BackHandler` (not `OnBackInvokedCallback`, which is for View-based apps only)
- Fixed `R6.9` code example: replaced `FontStyle.FontWeightBold` (does not exist — `FontStyle` only has `Normal`/`Italic`) with correct `fontWeightAdjustment >= 700` comparison
- Fixed `_sections.md` cross-reference table: corrected incorrect section pointers (several rows pointed to wrong sections)

**Web**
- Fixed WCAG citation: `SC 2.5.8` for 44px touch targets corrected to `SC 2.5.5` (Enhanced, AAA); SC 2.5.8 only requires 24px at AA
- Fixed `SC 2.3.3` compliance level: annotated as Level AAA, not AA; updated surrounding text to clarify "most rules are AA" rather than "all rules"
- Fixed `SC 2.4.11`/`SC 2.4.12` presentation: 2.4.11 is AA (required); 2.4.12 is AAA (enhanced) — now clearly annotated
- Fixed `Rule 7.6` description: `prefers-contrast: more` (macOS/iOS "Increase Contrast") and `prefers-contrast: forced` (Windows High Contrast Mode) are distinct OS features — clarified in prose

---

### New content

**Accessibility (all platforms)**

All Apple platforms now have a dedicated Accessibility section (CRITICAL impact) with evaluation checklist items:

| Platform | Rules added | Key coverage |
|----------|-------------|--------------|
| iPadOS | 9.1–9.7 | VoiceOver, Dynamic Type, pointer accessibility, Full Keyboard Access, Split View focus, Bold Text, Increase Contrast |
| macOS | 11.1–11.7 | VoiceOver, Full Keyboard Access, Reduce Motion, Bold Text (NSWorkspace), Increase Contrast |
| watchOS | W-AC-01–W-AC-06 | VoiceOver labels, Reduce Motion, accessibilityValue, Bold Text, Increase Contrast (SwiftUI-only APIs) |
| tvOS | ACCESS-01–ACCESS-07 | VoiceOver, Reduce Motion, Bold Text, Increase Contrast, Dynamic Type / Larger Text |
| visionOS | ACC-01–ACC-06 | VoiceOver for 3D objects, pointer/Switch Control, Reduce Motion, Bold Text, Increase Contrast |

**iOS**
- Added Rules 7.9–7.11: SF Symbols usage — rendering modes (monochrome/hierarchical/palette/multicolor), scale and weight matching, `symbolEffect` animations (iOS 17+)

**macOS**
- Added Section 10 (Popovers): 3 rules covering anchoring to source element, Esc dismissal, and sizing to content

**tvOS**
- Added `ACCESS-07`: Larger Text / Dynamic Type via `UIContentSizeCategory` and `UIFontMetrics`
- Added `R2.13`: Predictive back visual animation — interpolate using `BackEventCompat.progress` / `swipeEdge`

**Android**
- Added `R2.13`: Predictive back visual animation guidelines
- Added `R6.13`: `ExploreByTouchHelper` for custom canvas-drawn views (charts, games, custom pickers) — required for TalkBack accessibility on non-widget content
- Added `R6.9` API guidance: `Configuration.fontWeightAdjustment` (API 31+), `AccessibilityManager.isHighTextContrastEnabled()`, Material 3 automatic handling

**Web**
- Added Section 11 (Progressive Web Apps): 5 rules covering `manifest.json` required fields, `theme_color`/`background_color`, service worker registration, installability criteria, and app shortcuts
- Added Rule 7.6: `prefers-contrast` media query — `more` for macOS/iOS Increase Contrast, `forced` for Windows High Contrast Mode
- Added Rule 1.12: WCAG 2.5.3 Label in Name (Level A) — accessible name must contain visible text; `aria-label` must not replace visible text with different text

---

### Documentation and metadata

- Updated all `metadata.json` rule counts to accurate values
- Updated all `metadata.json` category counts after new sections were added
- Updated `README.md`: iOS "50+" → "67+"; total "300+" → "450+"
- Synced all `_sections.md` totals and section maps after every addition
- Updated all evaluation checklists to include newly added sections (Accessibility, Popovers, PWA, SF Symbols)
- Added "Never Do" quick-reference lists to all AGENTS.md files that were missing them (iPadOS, macOS, watchOS, tvOS, visionOS, Android, Web)
- Updated all AGENTS.md Rule Categories tables to include new sections with correct impact levels
- Fixed iPadOS Navigation impact: HIGH → CRITICAL (matches iOS and Android; sidebar navigation is the most foundational iPad HIG requirement)
- Updated `CLAUDE.md`: corrected description of `rules/` directory structure to match what actually exists (`_sections.md` rule index, not individual rule files)
