# Android Design Guidelines — Reference

## Design Evaluation Checklist

Use this checklist to evaluate Android UI implementations:

### Theme & Color
- [ ] Dynamic color enabled with static fallback
- [ ] All colors reference Material theme roles (no hardcoded hex)
- [ ] Light and dark themes both supported
- [ ] On-colors match their background color roles
- [ ] Custom colors generated from seed via Material Theme Builder

### Navigation
- [ ] Correct navigation component for screen size and destination count
- [ ] Navigation bar labels always visible
- [ ] Predictive back gesture opted in and handled
- [ ] Up vs Back behavior correct

### Layout
- [ ] All three window size classes supported
- [ ] Edge-to-edge with proper inset handling
- [ ] Content does not span full width on large screens
- [ ] Foldable hinge area respected

### Typography
- [ ] All text uses sp units
- [ ] All text references MaterialTheme.typography roles
- [ ] Tested at 200% font scale with no clipping
- [ ] Minimum 12sp body, 11sp labels

### Components
- [ ] At most one FAB per screen
- [ ] Top app bar connected to scroll behavior
- [ ] Snackbars used for non-critical feedback only
- [ ] Dialogs reserved for critical interruptions

### Accessibility
- [ ] All interactive elements have contentDescription
- [ ] All touch targets >= 48dp
- [ ] Color contrast >= 4.5:1 for text
- [ ] No information conveyed by color alone
- [ ] Full TalkBack traversal tested
- [ ] Switch Access and keyboard navigation work

### Gestures
- [ ] No interactive elements in system gesture zones
- [ ] All gesture actions have non-gesture alternatives
- [ ] Swipe-to-dismiss is undoable

### Notifications
- [ ] Separate channels for each notification type
- [ ] Appropriate importance levels
- [ ] Tap action navigates to relevant content

### Permissions
- [ ] Permissions requested in context, not at launch
- [ ] Rationale shown before permission request
- [ ] Graceful degradation on denial
- [ ] Photo Picker used instead of media permission

### System Integration
- [ ] Widgets use Glance API with dynamic color
- [ ] App shortcuts provided for common actions
- [ ] Deep links handled for public content

---

## Anti-Patterns

| Anti-Pattern | Why It Is Wrong | Correct Approach |
|-------------|----------------|------------------|
| Hardcoded color hex values | Breaks dynamic color and dark theme | Use `MaterialTheme.colorScheme` roles |
| Using `dp` for text size | Ignores user font scaling | Use `sp` units |
| Custom bottom navigation bar | Inconsistent with platform | Use Material `NavigationBar` |
| Navigation bar without labels | Violates Material guidelines | Always show labels |
| Dialog for non-critical info | Interrupts user unnecessarily | Use Snackbar or Bottom Sheet |
| FAB for secondary actions | Dilutes primary action prominence | One FAB for the primary action only |
| `onBackPressed()` override | Deprecated; breaks predictive back | Use `OnBackInvokedCallback` |
| Touch targets < 48dp | Accessibility violation | Ensure minimum 48x48dp |
| Permission request at launch | Users deny without context | Request in context with rationale |
| Pure black (#000000) dark theme | Eye strain; not Material 3 | Use Material surface color roles |
| Icon-only navigation bar | Users cannot identify destinations | Always include text labels |
| Full-width content on tablets | Wastes space; poor readability | Max width or list-detail layout |
| `READ_EXTERNAL_STORAGE` for photos | Unnecessary since Android 13 | Use Photo Picker API |
| Blocking UI on permission denial | Punishes the user | Graceful degradation |
| Manual color palette selection | Inconsistent tonal relationships | Use Material Theme Builder |
