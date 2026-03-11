# Web Design Guidelines Reference

Supplementary reference tables for the web design guidelines skill.

---

## ARIA Role Quick Reference

| Role | Purpose | Native Equivalent |
|------|---------|-------------------|
| `button` | Clickable action | `<button>` |
| `link` | Navigation | `<a href>` |
| `tab` / `tablist` / `tabpanel` | Tab interface | None |
| `dialog` | Modal | `<dialog>` |
| `alert` | Assertive live region | None |
| `status` | Polite live region | `<output>` |
| `navigation` | Nav landmark | `<nav>` |
| `main` | Main landmark | `<main>` |
| `complementary` | Aside landmark | `<aside>` |
| `search` | Search landmark | `<search>` (HTML5) |
| `img` | Image | `<img>` |
| `list` / `listitem` | List | `<ul>/<li>` |
| `heading` | Heading (with `aria-level`) | `<h1>`-`<h6>` |
| `menu` / `menuitem` | Menu widget | None |
| `tree` / `treeitem` | Tree view | None |
| `grid` / `row` / `gridcell` | Data grid | `<table>` |
| `progressbar` | Progress | `<progress>` |
| `slider` | Range input | `<input type="range">` |
| `switch` | Toggle | `<input type="checkbox">` |

**Rule**: Prefer native HTML over ARIA. Use ARIA only when no native element exists for the pattern.

---

## CSS Logical Properties Mapping

| Physical | Logical |
|----------|---------|
| `left` / `right` | `inline-start` / `inline-end` |
| `top` / `bottom` | `block-start` / `block-end` |
| `margin-left` | `margin-inline-start` |
| `padding-right` | `padding-inline-end` |
| `border-top-left-radius` | `border-start-start-radius` |
| `width` | `inline-size` |
| `height` | `block-size` |
| `text-align: left` | `text-align: start` |

---

## Common Anti-Patterns

| Anti-Pattern | Fix |
|--------------|-----|
| `<div onclick="...">` | Use `<button>` |
| `outline: none` without replacement | Use `:focus-visible` with custom outline |
| `placeholder` as label | Add a `<label>` element |
| `tabindex="5"` | Use `tabindex="0"` or natural order |
| `user-scalable=no` | Remove it |
| `font-size: 12px` | Use `font-size: 0.75rem` |
| Animating `width`/`height`/`top`/`left` | Animate `transform` and `opacity` |
| Disabling submit button | Validate on submit, show errors |
| Color alone for status | Add icon, text, or pattern |
| `margin-left` / `padding-right` | Use `margin-inline-start` / `padding-inline-end` |
| `<img>` without dimensions | Add `width` and `height` attributes |
| Hover-only disclosure | Add `:focus-within` and click handler |

---

## Evaluation Checklist

Use this checklist when building or reviewing web interfaces.

### Accessibility
- [ ] All images have appropriate `alt` text
- [ ] Color contrast meets 4.5:1 (text) and 3:1 (UI components)
- [ ] All interactive elements are keyboard accessible
- [ ] Focus indicators are visible (3:1 contrast, 2px minimum perimeter)
- [ ] Skip navigation link is present
- [ ] Form inputs have associated labels
- [ ] Error messages are linked to their inputs
- [ ] Dynamic content updates use ARIA live regions
- [ ] No content flashes more than 3 times per second
- [ ] Page has proper heading hierarchy (h1-h6, no skips)
- [ ] Landmarks are used correctly (main, nav, header, footer)

### Responsive
- [ ] No horizontal scrolling at 320px width
- [ ] Touch targets are at least 44x44px
- [ ] Viewport meta tag is present (no user-scalable=no)
- [ ] Layout works on mobile, tablet, and desktop
- [ ] Text is readable without zooming on mobile

### Forms
- [ ] All inputs have visible labels
- [ ] Autocomplete attributes are set for common fields
- [ ] Correct input types trigger correct mobile keyboards
- [ ] Error messages are clear and specific
- [ ] Required fields are indicated
- [ ] Submit button is not disabled

### Performance
- [ ] Below-fold images use `loading="lazy"`
- [ ] Images have explicit `width` and `height`
- [ ] Critical fonts are preloaded
- [ ] Third-party origins use `preconnect`
- [ ] Large JS bundles are code-split

### Motion and Theming
- [ ] `prefers-reduced-motion` is respected
- [ ] Animations use only `transform` and `opacity`
- [ ] Dark mode maintains contrast ratios
- [ ] `color-scheme` meta tag is present
- [ ] Theme uses CSS custom properties

### Internationalization
- [ ] `lang` attribute on `<html>`
- [ ] CSS logical properties used (not physical)
- [ ] Dates/numbers formatted with Intl APIs
- [ ] No text embedded in images
- [ ] Layout tested in RTL mode
