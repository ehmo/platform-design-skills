# iOS Design Guidelines -- Reference

## Quick Reference

| Need | Component | Notes |
|------|-----------|-------|
| Top-level sections (3-5) | `TabView` with `.tabItem` | Bottom tab bar, SF Symbols |
| Hierarchical drill-down | `NavigationStack` | Large title on root, inline on children |
| Self-contained task | `.sheet` | Swipe to dismiss, cancel/done buttons |
| Critical decision | `.alert` | 2 buttons preferred, max 3 |
| Secondary actions | `.contextMenu` | Long press; must also be accessible elsewhere |
| Scrolling content | `List` with `.insetGrouped` | 44pt min row, swipe actions |
| Text input | `TextField` / `TextEditor` | Label above, validation below |
| Selection (few options) | `Picker` | Segmented for 2-5, wheel for many |
| Selection (on/off) | `Toggle` | Aligned right in a list row |
| Search | `.searchable` | Suggestions, recent searches |
| Progress (known) | `ProgressView(value:total:)` | Show percentage or time remaining |
| Progress (unknown) | `ProgressView()` | Inline, never full-screen blocking |
| One-time location | `LocationButton` | No persistent permission needed |
| Sharing content | `ShareLink` | System share sheet |
| Haptic feedback | `UIImpactFeedbackGenerator` | `.light`, `.medium`, `.heavy` |
| Destructive action | `Button(role: .destructive)` | Red tint, confirm via alert |

---

## Evaluation Checklist

Use this checklist to audit an iPhone app for HIG compliance:

### Layout & Safe Areas
- [ ] All touch targets are at least 44x44pt
- [ ] No content is clipped under status bar, Dynamic Island, or home indicator
- [ ] Primary actions are in the bottom half of the screen (thumb zone)
- [ ] Layout adapts from iPhone SE to Pro Max without breaking
- [ ] Spacing aligns to the 8pt grid

### Navigation
- [ ] Tab bar is used for 3-5 top-level sections
- [ ] No hamburger/drawer menus
- [ ] Primary views use large titles
- [ ] Swipe-from-left-edge back navigation works throughout
- [ ] State is preserved when switching tabs

### Typography
- [ ] All text uses built-in text styles or `UIFontMetrics`-scaled custom fonts
- [ ] Dynamic Type is supported up to accessibility sizes
- [ ] Layouts reflow at large text sizes (no truncation of essential text)
- [ ] Minimum text size is 11pt

### Color & Dark Mode
- [ ] App uses semantic system colors or provides light/dark asset variants
- [ ] Dark Mode looks intentional (not just inverted)
- [ ] No information conveyed by color alone
- [ ] Text contrast meets 4.5:1 (normal) or 3:1 (large)
- [ ] Single accent color for interactive elements

### Accessibility
- [ ] VoiceOver reads all screens logically with meaningful labels
- [ ] Bold Text preference is respected
- [ ] Reduce Motion disables decorative animations
- [ ] Increase Contrast variant exists for custom colors
- [ ] All gestures have alternative access paths

### Components
- [ ] Alerts are used only for critical decisions
- [ ] Sheets have a dismiss path (button and/or swipe)
- [ ] List rows are at least 44pt tall
- [ ] Tab bar is never hidden during navigation
- [ ] Destructive buttons use the `.destructive` role

### Privacy
- [ ] Permissions are requested in context, not at launch
- [ ] Custom explanation shown before each system permission dialog
- [ ] Sign in with Apple offered alongside other providers
- [ ] App is usable without an account for basic features
- [ ] ATT prompt is shown if tracking, and denial is respected

### System Integration
- [ ] Widgets show glanceable, up-to-date information
- [ ] App content is indexed for Spotlight
- [ ] Share Sheet is available for shareable content
- [ ] App handles interruptions (calls, background, Siri) gracefully

---

## Anti-Patterns

These are common mistakes that violate the iOS Human Interface Guidelines. Never do these:

1. **Hamburger menus** -- Use a tab bar. Hamburger menus hide navigation and reduce feature discoverability by up to 50%.

2. **Custom back buttons that break swipe-back** -- If you replace the back button, ensure the swipe-from-left-edge gesture still works via `NavigationStack`.

3. **Full-screen blocking spinners** -- Use skeleton views or inline progress indicators. Blocking spinners make the app feel frozen.

4. **Splash screens with logos** -- The launch screen must mirror the first screen of the app. Branding delays feel artificial.

5. **Requesting all permissions at launch** -- Asking for camera, location, notifications, and contacts on first launch guarantees most will be denied.

6. **Hardcoded font sizes** -- Use text styles. Hardcoded sizes ignore Dynamic Type and accessibility preferences, breaking the app for millions of users.

7. **Using only color to indicate state** -- Red/green for valid/invalid excludes colorblind users. Always pair with icons or text.

8. **Alerts for non-critical information** -- Alerts interrupt flow and require dismissal. Use banners, toasts, or inline messages for tips and non-critical information.

9. **Hiding the tab bar on push** -- Tab bars should remain visible throughout navigation within a tab. Hiding them disorients users.

10. **Ignoring safe areas** -- Using `.ignoresSafeArea()` on content views causes text and buttons to disappear under the notch, Dynamic Island, or home indicator.

11. **Non-dismissable modals** -- Every modal must have a clear dismiss path (close button, cancel, swipe down). Trapping users in a modal is hostile.

12. **Custom gestures without alternatives** -- A three-finger swipe for undo is unusable for many people. Provide a visible button or menu item as well.

13. **Tiny touch targets** -- Buttons and links smaller than 44pt cause mis-taps, especially in lists and toolbars.

14. **Stacked modals** -- Presenting a sheet on top of a sheet on top of a sheet creates navigation confusion. Use navigation within a single modal instead.

15. **Dark Mode as an afterthought** -- Using hardcoded colors means the app is either broken in Dark Mode or light mode. Always use semantic colors.
