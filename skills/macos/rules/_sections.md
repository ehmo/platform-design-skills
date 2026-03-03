# macOS Design Guidelines — Section Index

Quick-reference for all 9 categories and 40+ rules. See `../SKILL.md` for full details, code examples, and rationale.

---

## Section 1: Menu Bar [CRITICAL]

| Rule | Summary |
|------|---------|
| 1.1 | Provide standard menus: App, File, Edit, View, Window, Help |
| 1.2 | Keyboard shortcut for every menu item; follow standard conventions |
| 1.3 | Dynamic menu updates: disable unavailable items, update titles contextually |
| 1.4 | Right-click context menus on all interactive elements |
| 1.5 | App menu must contain About, Settings, Services, Hide, Quit |

**Key principle:** The menu bar is the primary command discovery surface on Mac. Every action in the app must be reachable through the menu bar.

---

## Section 2: Windows [CRITICAL]

| Rule | Summary |
|------|---------|
| 2.1 | Resizable windows with sensible minimum sizes |
| 2.2 | Support native fullscreen and Split View |
| 2.3 | Support multiple simultaneous windows |
| 2.4 | Title bar shows document name, proxy icon, edited state |
| 2.5 | Persist window position, size, and state across launches |
| 2.6 | Never hide or reposition traffic light buttons |

**Key principle:** Users control window size and position. Never fight window management.

---

## Section 3: Toolbars [HIGH]

| Rule | Summary |
|------|---------|
| 3.1 | Use unified title bar + toolbar style |
| 3.2 | Allow user customization of toolbar items |
| 3.3 | Segmented controls for view mode switching |
| 3.4 | Search field in trailing area of toolbar |
| 3.5 | Toolbar items have both icon (SF Symbol) and text label |

**Key principle:** Toolbars provide fast access to frequent actions and should be user-configurable.

---

## Section 4: Sidebars [HIGH]

| Rule | Summary |
|------|---------|
| 4.1 | Leading edge, collapsible, with persistent state |
| 4.2 | Source list style with translucent vibrancy |
| 4.3 | Outline views for hierarchical content |
| 4.4 | Drag-to-reorder for user-arranged items |
| 4.5 | Badge counts for unread/pending indicators |

**Key principle:** Sidebars are the primary navigation pattern for Mac apps with multiple content sections.

---

## Section 5: Keyboard [CRITICAL]

| Rule | Summary |
|------|---------|
| 5.1 | Cmd+key shortcuts for all actions; follow modifier conventions |
| 5.2 | Full Tab/arrow key navigation between and within controls |
| 5.3 | Esc dismisses popovers, sheets, dialogs; cancels operations |
| 5.4 | Return/Enter activates the default (blue) button |
| 5.5 | Delete key removes selected items; Cmd+Z undoes |
| 5.6 | Space bar invokes Quick Look for previewable items |
| 5.7 | Arrow keys navigate lists, grids, disclosure groups |

**Key principle:** Every mouse action must have a keyboard equivalent. Power users live on the keyboard.

---

## Section 6: Pointer and Mouse [HIGH]

| Rule | Summary |
|------|---------|
| 6.1 | Visible hover states on all interactive elements |
| 6.2 | Right-click context menus on every interactive element |
| 6.3 | Drag and drop for reordering, moving, importing, exporting |
| 6.4 | Support smooth trackpad and discrete mouse wheel scrolling |
| 6.5 | Cursor changes to indicate affordance (pointer, I-beam, crosshair, resize) |
| 6.6 | Cmd+Click for non-contiguous, Shift+Click for range selection |

**Key principle:** Mac is a pointer-driven platform. Every element must respond to hover, click, right-click, and drag.

---

## Section 7: Notifications and Alerts [MEDIUM]

| Rule | Summary |
|------|---------|
| 7.1 | Notifications only for events outside the app or requiring action |
| 7.2 | Recurring alerts offer "Do not show again" suppression |
| 7.3 | Never show alerts for successful routine operations |
| 7.4 | Dock badge for notification counts; clear promptly |

**Key principle:** Respect user attention. Only interrupt when genuinely necessary.

---

## Section 8: System Integration [MEDIUM]

| Rule | Summary |
|------|---------|
| 8.1 | High-quality Dock icon; Dock right-click menu with quick actions |
| 8.2 | Index app content in Spotlight via Core Spotlight |
| 8.3 | Quick Look Preview Extension for custom file types |
| 8.4 | Share menu for sending content to other apps |
| 8.5 | Services menu registration for receiving content |
| 8.6 | App Intents for Shortcuts; AppleScript/JXA scripting support |

**Key principle:** Mac apps exist in a rich ecosystem. Deep integration makes an app feel truly native.

---

## Section 9: Visual Design [HIGH]

| Rule | Summary |
|------|---------|
| 9.1 | SF Pro system font at semantic type sizes; SF Mono for code |
| 9.2 | Vibrancy and system materials for sidebar/toolbar backgrounds |
| 9.3 | System accent color for selection and emphasis; no brand override on standard controls |
| 9.4 | Full Dark Mode support with semantic colors |
| 9.5 | Respect "Reduce transparency" accessibility setting |
| 9.6 | 20pt margins, 8pt control spacing, 20pt group spacing |

**Key principle:** Use system-provided colors, fonts, and materials. Your app should feel like it belongs on the Mac.

---

## Priority Summary

| Priority | Sections | Rule Count |
|----------|----------|------------|
| CRITICAL | Menu Bar, Windows, Keyboard | 18 rules |
| HIGH | Toolbars, Sidebars, Pointer/Mouse, Visual Design | 22 rules |
| MEDIUM | Notifications, System Integration | 10 rules |
| **Total** | **9 sections** | **50 rules** |

---

## Cross-Cutting Concerns

These principles apply across all sections:

- **Undo everywhere** — Cmd+Z must work for any modifying action (Sections 1, 5)
- **Keyboard + pointer parity** — Every mouse action has a keyboard shortcut (Sections 1, 5, 6)
- **Respect system settings** — Dark Mode, accent color, transparency, font size (Section 9)
- **Consistent with platform** — No iOS patterns (tab bars, hamburger menus, FABs) on Mac (Anti-patterns)
- **User control** — Customizable toolbars, resizable windows, collapsible sidebars (Sections 2, 3, 4)
