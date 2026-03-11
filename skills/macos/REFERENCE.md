# macOS Design Guidelines — Reference

Quick reference tables and checklists extracted from the main skill.

---

## Keyboard Shortcut Quick Reference

### Navigation
| Shortcut | Action |
|----------|--------|
| Cmd+N | New window/document |
| Cmd+O | Open |
| Cmd+W | Close window/tab |
| Cmd+Q | Quit app |
| Cmd+, | Settings/Preferences |
| Cmd+Tab | Switch apps |
| Cmd+` | Switch windows within app |
| Cmd+T | New tab |

### Editing
| Shortcut | Action |
|----------|--------|
| Cmd+Z | Undo |
| Cmd+Shift+Z | Redo |
| Cmd+X / C / V | Cut / Copy / Paste |
| Cmd+A | Select All |
| Cmd+D | Duplicate |
| Cmd+F | Find |
| Cmd+G | Find Next |
| Cmd+Shift+G | Find Previous |
| Cmd+E | Use Selection for Find |

### View
| Shortcut | Action |
|----------|--------|
| Cmd+Ctrl+F | Toggle fullscreen |
| Cmd+Ctrl+S | Toggle sidebar |
| Cmd+0 | Show/hide toolbar |
| Cmd++ / Cmd+- | Zoom in/out |
| Cmd+0 | Actual size |

---

## Evaluation Checklist

Before shipping a Mac app, verify:

### Menu Bar
- [ ] App has a complete menu bar with standard menus
- [ ] All actions have keyboard shortcuts
- [ ] Menu items dynamically update (enable/disable, title changes)
- [ ] Context menus on all interactive elements
- [ ] App menu has About, Settings, Hide, Quit

### Windows
- [ ] Windows are freely resizable with sensible minimums
- [ ] Fullscreen and Split View work
- [ ] Multiple windows supported (if appropriate)
- [ ] Window position and size persist across launches
- [ ] Traffic light buttons visible and functional
- [ ] Document title and edited state shown (if document-based)

### Toolbars
- [ ] Toolbar present with common actions
- [ ] Toolbar is user-customizable
- [ ] Search field available in toolbar

### Sidebars
- [ ] Sidebar for navigation (if app has multiple sections)
- [ ] Sidebar is collapsible
- [ ] Source list style with vibrancy

### Keyboard
- [ ] Full keyboard navigation (Tab, arrows, Enter, Esc)
- [ ] Cmd+Z undo for all destructive actions
- [ ] Space for Quick Look previews
- [ ] Delete key removes selected items
- [ ] No keyboard traps (user can always Tab out)

### Pointer
- [ ] Hover states on interactive elements
- [ ] Right-click context menus everywhere
- [ ] Drag and drop for content manipulation
- [ ] Cmd+Click for multi-selection
- [ ] Appropriate cursor changes

### Notifications
- [ ] Notifications only for important events
- [ ] Alerts have suppression option for recurring ones
- [ ] No modal alerts for routine operations

### System Integration
- [ ] High-quality Dock icon
- [ ] Content indexed in Spotlight (if applicable)
- [ ] Share menu works
- [ ] App Intents for Shortcuts

### Visual Design
- [ ] System fonts at semantic sizes
- [ ] Dark Mode fully supported
- [ ] System accent color respected
- [ ] Translucency respects accessibility setting
- [ ] Consistent spacing on 8pt grid

---

## Anti-Patterns

| # | Anti-Pattern | Why It Fails |
|---|-------------|--------------|
| 1 | No menu bar | Menu bar is the primary command discovery mechanism on Mac |
| 2 | Hamburger menus | The menu bar already serves this purpose; hamburger signals an iOS port |
| 3 | Bottom tab bars | Mac uses sidebars and toolbars; use document tabs if needed |
| 4 | Large touch-sized targets | Mac controls should be 22-28pt height; users have precise pointer input |
| 5 | Floating action buttons | Place primary actions in toolbar or menu bar instead |
| 6 | Sheet for every action | Use popovers or inline editing; reserve sheets for multi-step workflows |
| 7 | Custom window chrome | Never replace standard title bar or traffic lights |
| 8 | Ignoring keyboard | Every common action must have a keyboard equivalent |
| 9 | Single-window only | Support Cmd+N for new windows unless genuinely single-purpose |
| 10 | Fixed window size | Windows must be resizable; users have 13" to 32" displays |
| 11 | No Cmd+Z undo | Every destructive action must be undoable |
| 12 | Notification spam | Only notify for events that genuinely need attention |
| 13 | Ignoring Dark Mode | Always support and test both appearances |
| 14 | Hardcoded colors | Use semantic system colors that adapt to mode and accessibility |
| 15 | No drag and drop | Mac is a drag-and-drop platform; users expect to drag visible content |
