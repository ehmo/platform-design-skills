# iOS Design Skill

Apple Human Interface Guidelines for iPhone apps — layout, navigation, typography, accessibility, and system integration rules with SwiftUI/UIKit examples.

**Reference:** https://developer.apple.com/design/human-interface-guidelines

## Rule Categories

| # | Category | Impact | Rules |
|---|----------|--------|-------|
| 1 | Layout & Safe Areas | CRITICAL | Touch targets, safe areas, thumb zone, screen sizes |
| 2 | Navigation | CRITICAL | Tab bars, large titles, back swipe, state preservation |
| 3 | Typography & Dynamic Type | HIGH | Text styles, Dynamic Type, UIFontMetrics, SF Pro |
| 4 | Color & Dark Mode | HIGH | Semantic colors, contrast, P3 gamut, accent color |
| 5 | Accessibility | CRITICAL | VoiceOver, Bold Text, Reduce Motion, Switch Control |
| 6 | Gestures & Input | HIGH | Standard gestures, system gesture protection, input methods |
| 7 | Components | HIGH | Buttons, alerts, sheets, lists, tab bars, search, menus |
| 8 | Patterns | MEDIUM | Onboarding, loading, launch, modality, feedback |
| 9 | Privacy & Permissions | HIGH | Contextual requests, Sign in with Apple, ATT |
| 10 | System Integration | MEDIUM | Widgets, App Shortcuts, Spotlight, Live Activities |

## Never Do

- Never use hamburger menus — use a tab bar
- Never override swipe-from-left-edge back navigation
- Never hardcode text sizes — support Dynamic Type
- Never use only color to convey information
- Never request all permissions at launch
- Never place primary actions at the top of the screen (outside thumb zone)
- Never clip content under the status bar, home indicator, or Dynamic Island
- Never use blocking spinner overlays for loading states
- Never show splash screen logos — match the first screen of the app
- Never hide the tab bar during navigation within a tab
