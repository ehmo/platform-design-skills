# iPadOS Design Guidelines -- Reference

## Evaluation Checklist

Use this checklist to verify iPad-readiness:

### Layout & Multitasking
- [ ] App uses adaptive layout with `horizontalSizeClass`
- [ ] Tested at all Split View ratios (1/3, 1/2, 2/3)
- [ ] Tested in Slide Over (compact width)
- [ ] Stage Manager: resizes fluidly to arbitrary dimensions
- [ ] Multiple scenes/windows supported
- [ ] Both orientations (portrait and landscape) work correctly
- [ ] No content clipped at any size
- [ ] Safe areas respected on all iPad models

### Navigation
- [ ] Sidebar visible in regular width
- [ ] Tab bar used in compact width
- [ ] Detail view shows placeholder when no selection
- [ ] Toolbar items placed at top, not bottom
- [ ] Three-column layout used where appropriate

### Pointer & Trackpad
- [ ] Hover effects on all interactive elements
- [ ] Right-click context menus available
- [ ] Pointer cursor adapts to content (I-beam for text, etc.)
- [ ] Click-and-drag works for reordering

### Keyboard
- [ ] Cmd+key shortcuts for all major actions
- [ ] Shortcuts appear in Cmd-hold overlay
- [ ] Tab key navigates between form fields
- [ ] No system shortcut conflicts
- [ ] Arrow keys navigate lists and grids
- [ ] Return/Enter activates default action

### Apple Pencil
- [ ] Scribble works in all text fields
- [ ] Drawing apps support pressure and tilt
- [ ] Double-tap interaction handled (if applicable)

### Drag and Drop
- [ ] Content can be dragged out to other apps
- [ ] Content can be dropped in from other apps
- [ ] Multi-item drag supported
- [ ] Visual feedback for all drag states

### External Display
- [ ] Extended content shown (not just mirror)
- [ ] Graceful handling of connect/disconnect

---

## Anti-Patterns

- **Scale Up iPhone Layouts** -- Always redesign for the larger canvas with multi-column layouts.
- **Disable Multitasking** -- Never opt out. Support Split View and Slide Over.
- **Ignore the Keyboard** -- Provide shortcuts for all frequent actions.
- **Use iPhone-Style Bottom Tab Bars in Regular Width** -- Convert to sidebar with `.sidebarAdaptable`.
- **Show Popovers as Full-Screen Sheets** -- Anchor popovers to source elements as floating panels.
- **Ignore Pointer Hover States** -- Add `.hoverEffect()` to all custom interactive elements.
- **Hardcode Dimensions** -- Use flexible frames and `GeometryReader` for dynamic sizing.
- **Forget Drag and Drop** -- At minimum, support dragging text, images, and URLs in and out.
- **Override System Keyboard Shortcuts** -- Check Apple's reserved shortcuts list before assigning.
- **Present Dense Content Without Scrolling** -- Content should still scroll when it exceeds the visible area.
