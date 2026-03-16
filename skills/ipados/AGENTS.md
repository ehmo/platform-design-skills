# iPadOS Design Guidelines Skill

iPad-specific HIG rules extending iOS patterns for the larger, multitasking-capable canvas.

**Reference**: [Apple HIG - Designing for iPadOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados)

## Categories & Impact

| # | Category | Impact | Key Focus |
|---|----------|--------|-----------|
| 1 | Responsive Layout | CRITICAL | Adaptive layouts, size classes, column-based design |
| 2 | Multitasking | CRITICAL | Split View, Slide Over, Stage Manager, resizable windows |
| 3 | Navigation | CRITICAL | Sidebar, three-column layout, toolbar placement |
| 4 | Pointer & Trackpad | HIGH | Hover effects, magnetism, right-click, drag and drop |
| 5 | Keyboard | HIGH | Cmd shortcuts, discoverability overlay, tab navigation |
| 6 | Apple Pencil | MEDIUM | Scribble, hover detection, PencilKit |
| 7 | Drag and Drop | HIGH | Inter-app, multi-item, spring-loaded, Universal Control |
| 8 | External Display | MEDIUM | Extended content, AirPlay, display lifecycle |
| 9 | Accessibility | CRITICAL | VoiceOver labels, Dynamic Type, pointer accessibility, Full Keyboard Access |

## Key Differentiators from iOS

- **Sidebar replaces tab bar** in regular width size class
- **Multitasking is mandatory** -- app must function at all split sizes
- **Pointer support expected** -- hover states, magnetism, right-click menus
- **Keyboard shortcuts required** -- Cmd+key for all major actions with discoverability overlay
- **Drag and drop across apps** -- first-class interaction pattern
- **Stage Manager** -- freely resizable windows, multiple scenes
- **Toolbar at top** instead of bottom navigation
- **Three-column layouts** for deep hierarchies

## Never Do

- Never scale up an iPhone layout to fill the iPad screen — redesign for the larger canvas
- Never opt out of multitasking — every app must work in Split View and Slide Over
- Never use bottom tab bars in regular width — use sidebar navigation
- Never show popovers as full-screen sheets — anchor popovers to their source element
- Never hardcode pixel dimensions for specific iPad models
- Never omit hover states for interactive elements — trackpad users need visual feedback
- Never override system keyboard shortcuts (Cmd+H, Cmd+Tab, Cmd+Space)
- Never ignore drag and drop — at minimum support dragging text, images, and URLs
- Never block the keyboard from dismissing modal sheets on iPad
- Never present iPhone-style modals when a popover or inspector is more appropriate
- Never omit accessibility labels on icon-only buttons or custom interactive elements
- Never disable Dynamic Type scaling or clamp text size — iPad users rely on large type too
- Never create keyboard focus paths that trap or skip interactive elements in Split View
