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

## Key Principles

- Mac users expect menu bars, keyboard shortcuts, and multi-window support
- Every destructive action needs Cmd+Z undo
- Toolbars and sidebars should be user-customizable
- Respect system appearance (Dark Mode, accent color, font size)
- Support drag and drop everywhere it makes sense
- Desktop apps are power-user tools — don't hide functionality behind discoverability walls
