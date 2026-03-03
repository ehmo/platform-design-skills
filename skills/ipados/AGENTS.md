# iPadOS Design Guidelines Skill

iPad-specific HIG rules extending iOS patterns for the larger, multitasking-capable canvas.

**Reference**: [Apple HIG - Designing for iPadOS](https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados)

## Categories & Impact

| # | Category | Impact | Key Focus |
|---|----------|--------|-----------|
| 1 | Responsive Layout | CRITICAL | Adaptive layouts, size classes, column-based design |
| 2 | Multitasking | CRITICAL | Split View, Slide Over, Stage Manager, resizable windows |
| 3 | Navigation | HIGH | Sidebar, three-column layout, toolbar placement |
| 4 | Pointer & Trackpad | HIGH | Hover effects, magnetism, right-click, drag and drop |
| 5 | Keyboard | HIGH | Cmd shortcuts, discoverability overlay, tab navigation |
| 6 | Apple Pencil | MEDIUM | Scribble, hover detection, PencilKit |
| 7 | Drag and Drop | HIGH | Inter-app, multi-item, spring-loaded, Universal Control |
| 8 | External Display | MEDIUM | Extended content, AirPlay, display lifecycle |

## Key Differentiators from iOS

- **Sidebar replaces tab bar** in regular width size class
- **Multitasking is mandatory** -- app must function at all split sizes
- **Pointer support expected** -- hover states, magnetism, right-click menus
- **Keyboard shortcuts required** -- Cmd+key for all major actions with discoverability overlay
- **Drag and drop across apps** -- first-class interaction pattern
- **Stage Manager** -- freely resizable windows, multiple scenes
- **Toolbar at top** instead of bottom navigation
- **Three-column layouts** for deep hierarchies
