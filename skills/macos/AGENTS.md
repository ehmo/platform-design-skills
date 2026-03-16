# macOS Design Guidelines — Agent Instructions

## Purpose

This skill provides Apple Human Interface Guidelines for macOS. Apply these rules when building, reviewing, or designing Mac apps using SwiftUI or AppKit.

## When to Apply

- Building any macOS application
- Reviewing Mac UI code or designs
- Implementing menu bars, toolbars, sidebars, or window management
- Adding keyboard shortcuts or pointer interactions
- Porting iOS apps to Mac via Catalyst or Designed for iPad
- Evaluating desktop app usability

## How to Use

1. Read `SKILL.md` for the full rule set with code examples
2. Read `rules/_sections.md` for the categorized quick-reference
3. Use the evaluation checklist in SKILL.md before shipping

## Priority

Rules marked CRITICAL must never be skipped. Rules marked HIGH should be followed unless there is a documented reason. Rules marked MEDIUM are strong recommendations.

## Rule Categories

| # | Category | Impact |
|---|----------|--------|
| 1 | Menu Bar | CRITICAL |
| 2 | Windows | CRITICAL |
| 3 | Toolbars | HIGH |
| 4 | Sidebars | HIGH |
| 5 | Keyboard | CRITICAL |
| 6 | Pointer and Mouse | HIGH |
| 7 | Notifications and Alerts | MEDIUM |
| 8 | System Integration | MEDIUM |
| 9 | Visual Design | HIGH |
| 10 | Popovers | MEDIUM |
| 11 | Accessibility | CRITICAL |

## Key Principles

- Mac users expect menu bars, keyboard shortcuts, and multi-window support
- Every destructive action needs Cmd+Z undo
- Toolbars and sidebars should be user-customizable
- Respect system appearance (Dark Mode, accent color, font size)
- Support drag and drop everywhere it makes sense
- Desktop apps are power-user tools — don't hide functionality behind discoverability walls

## Never Do

- Never ship without a menu bar
- Never use hamburger menus — use the menu bar or a sidebar
- Never place a tab bar at the bottom of the screen
- Never hardcode colors — use semantic system colors for Dark Mode compatibility
- Never build non-resizable main windows
- Never omit keyboard shortcuts for common actions
- Never block full keyboard navigation — no keyboard traps
- Never override traffic light buttons or window chrome
- Never use floating action buttons — use toolbar and menu bar actions
- Never ignore VoiceOver — every control needs an accessibility label
