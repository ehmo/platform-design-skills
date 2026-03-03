# Web Platform Design Rules (Sectioned)

Load individual sections as needed. Each section is self-contained.

---

<!-- SECTION: accessibility -->
## Accessibility / WCAG [CRITICAL]

### Rules

1. **Use semantic HTML elements.** Use `<main>`, `<nav>`, `<header>`, `<footer>`, `<article>`, `<section>`, `<aside>`, `<figure>`, `<dialog>`, `<details>` for their intended purpose. Never use `<div onclick>` when `<button>` exists.

2. **Add ARIA labels to interactive elements without visible text.** Icon buttons need `aria-label`. Groups need `aria-labelledby`. Do not add ARIA when visible text already provides the name.

3. **Ensure keyboard navigation.** All interactive elements reachable via Tab. Custom widgets need `tabindex="0"` and keydown handlers. Trap focus inside modals. Never use `tabindex` > 0.

4. **Provide visible focus indicators.** Use `:focus-visible` with 3px outline and 2px offset. WCAG 2.2 requires minimum 2px perimeter area with 3:1 contrast.

5. **Include skip navigation links.** Add `<a href="#main-content" class="skip-link">Skip to main content</a>` before the nav. Visually hidden until focused.

6. **Write appropriate alt text.** Informative images: describe content. Decorative images: `alt=""`. Functional images: describe the action. Complex images: short alt + linked long description.

7. **Meet color contrast ratios.** Normal text: 4.5:1. Large text (>=24px or >=18.66px bold): 3:1. UI components and graphics: 3:1. Never rely on color alone.

8. **Associate labels with form inputs.** Use `<label for="id">` or wrap input in `<label>`. Never use placeholder as the only label.

9. **Identify errors in text and link to inputs.** Use `aria-describedby` or `aria-errormessage` with `aria-invalid="true"`. Error text must describe how to fix the problem.

10. **Use ARIA live regions for dynamic updates.** `aria-live="polite"` for non-urgent updates. `role="alert"` for time-sensitive messages. `role="status"` for status messages.

11. **Prefer native HTML over ARIA roles.** Use `<button>` not `<div role="button">`. Use `<nav>` not `<div role="navigation">`. ARIA is a supplement, not a replacement.

<!-- /SECTION: accessibility -->

---

<!-- SECTION: responsive -->
## Responsive Design [CRITICAL]

### Rules

1. **Build mobile-first.** Base styles for smallest viewport. Add complexity with `min-width` media queries.

2. **Use fluid sizing with `clamp()`, `min()`, `max()`.** Fluid typography: `font-size: clamp(1.75rem, 1.2rem + 2vw, 3rem)`. Fluid containers: `width: min(90%, 72rem)`.

3. **Use container queries for component-level responsiveness.** Set `container-type: inline-size` on wrappers. Use `@container` for layout changes based on available space.

4. **Set breakpoints at content, not device widths.** Break where your layout breaks. Common starting points: 30rem, 48rem, 64rem, 80rem.

5. **Ensure touch targets are 44x44px minimum.** Expand small icons with `::after` pseudo-element and negative `inset`. Maintain 24px spacing between adjacent targets.

6. **Include viewport meta tag.** `<meta name="viewport" content="width=device-width, initial-scale=1">`. Never use `maximum-scale=1` or `user-scalable=no`.

7. **Prevent horizontal scrolling.** Set `max-width: 100%; height: auto` on images/video/iframes. Use `overflow-wrap: break-word` for long text. Wrap tables in `overflow-x: auto` container.

<!-- /SECTION: responsive -->

---

<!-- SECTION: forms -->
## Forms [HIGH]

### Rules

1. **Label every input.** Use `<label for="id">` with matching `id` on the input. Every field needs a visible, programmatically associated label.

2. **Set autocomplete attributes.** Use `autocomplete="email"`, `autocomplete="tel"`, `autocomplete="name"`, `autocomplete="street-address"`, etc. Required by WCAG SC 1.3.5.

3. **Use correct input types.** `type="email"` for email, `type="tel"` for phone, `type="url"` for URLs, `inputmode="numeric"` for numeric data without spinners.

4. **Validate inline on blur.** Show errors after the user leaves a field, not on every keystroke. Use `aria-invalid` and `aria-describedby` to link errors.

5. **Group related fields with fieldset/legend.** Radio groups, checkbox groups, and address blocks belong in `<fieldset>` with a `<legend>`.

6. **Indicate required fields.** Use `required` attribute. Show a visible marker (asterisk or "(required)" text). If most fields are required, indicate optional fields instead.

7. **Keep submit buttons enabled.** Validate on submit and show errors. Disabled buttons fail to explain why the user cannot proceed.

<!-- /SECTION: forms -->

---

<!-- SECTION: typography -->
## Typography [HIGH]

### Rules

1. **Use system font stacks or web fonts with fallbacks.** System: `font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`. Web fonts: add `font-display: swap`.

2. **Use relative units.** `rem` for font sizes and spacing. `em` for component-relative sizing. Never set `font-size` in `px` on body text.

3. **Set body line height to at least 1.5.** Headings can use 1.2. Paragraph spacing at least 2x font size. Required by WCAG SC 1.4.12.

4. **Limit line length to ~75 characters.** Use `max-width: 75ch` or `max-width: 40rem` on prose containers.

5. **Use proper typographic details.** Curly quotes via CSS `quotes` property. Tabular numbers (`font-variant-numeric: tabular-nums`) for data columns.

6. **Maintain heading hierarchy.** `h1` through `h6` in order, no skipping. One `h1` per page. Style headings with classes if visual size differs from semantic level.

<!-- /SECTION: typography -->

---

<!-- SECTION: performance -->
## Performance [HIGH]

### Rules

1. **Lazy load below-fold images.** Use `loading="lazy"`. Above-fold hero images get `fetchpriority="high"`.

2. **Set explicit image dimensions.** Add `width` and `height` attributes to prevent Cumulative Layout Shift (CLS).

3. **Use resource hints.** `<link rel="preconnect">` for third-party origins. `<link rel="preload">` for critical fonts and CSS. `<link rel="dns-prefetch">` for non-critical origins.

4. **Code-split JavaScript.** Use dynamic `import()` for route-based and interaction-based splitting. Load heavy libraries only when needed.

5. **Virtualize long lists.** Render only visible rows plus a small buffer for lists exceeding a few hundred items.

6. **Avoid layout thrashing.** Batch all DOM reads, then batch all DOM writes. Never interleave reads and writes in a loop.

7. **Use `will-change` sparingly.** Apply only to elements that will animate. Remove after animation completes. Never apply globally.

<!-- /SECTION: performance -->

---

<!-- SECTION: animation -->
## Animation and Motion [MEDIUM]

### Rules

1. **Respect `prefers-reduced-motion`.** Wrap all animations in a media query check. Set `animation-duration: 0.01ms` and `transition-duration: 0.01ms` for reduced motion preference.

2. **Animate only compositor-friendly properties.** Use `transform` and `opacity`. Avoid animating `width`, `height`, `top`, `left`, `margin`, or `padding`.

3. **No flashing content above 3Hz.** Content that flashes more than 3 times per second can trigger seizures. WCAG SC 2.3.1.

4. **Use transitions for state changes.** Hover, focus, open/close, and visibility changes should transition smoothly (150-300ms).

5. **Motion must be meaningful.** Animate to communicate state, guide attention, or show spatial relationships. Never animate purely for decoration.

<!-- /SECTION: animation -->

---

<!-- SECTION: dark-mode -->
## Dark Mode and Theming [MEDIUM]

### Rules

1. **Detect system preference.** Use `@media (prefers-color-scheme: dark)` to switch theme tokens.

2. **Define themes with CSS custom properties.** All colors, shadows, and surfaces as `--custom-properties`. Toggle entire themes by redefining variables.

3. **Set the `color-scheme` meta tag.** `<meta name="color-scheme" content="light dark">`. Also set `color-scheme: light dark` in CSS for native form controls.

4. **Verify contrast in both modes.** Dark mode commonly fails contrast on secondary text and disabled states. Re-check all ratios.

5. **Adapt images to theme.** Use `<picture>` with `media="(prefers-color-scheme: dark)"` for alternate assets. Use `filter: brightness()` for simple adjustments.

<!-- /SECTION: dark-mode -->

---

<!-- SECTION: navigation -->
## Navigation and State [MEDIUM]

### Rules

1. **URL reflects state.** Every meaningful view has a unique URL. Use `URLSearchParams` and `history.pushState` for filters, tabs, and pagination.

2. **Support browser back/forward.** Handle `popstate` events to restore state from the URL.

3. **Mark active navigation items.** Use `aria-current="page"` on the active link. Style with `[aria-current="page"]` selector.

4. **Add breadcrumbs for deep hierarchies.** Use `<nav aria-label="Breadcrumb">` with an ordered list. Mark current page with `aria-current="page"`.

5. **Manage scroll restoration.** Set `history.scrollRestoration = 'manual'` in SPAs. Save and restore scroll position on navigation.

<!-- /SECTION: navigation -->

---

<!-- SECTION: touch -->
## Touch and Interaction [MEDIUM]

### Rules

1. **Use `touch-action` for scroll control.** `pan-y` for vertical-only scroll areas. `pan-x` for carousels. `none` for canvas/map elements.

2. **Disable tap highlight.** Set `-webkit-tap-highlight-color: transparent` on buttons and links; provide your own active state instead.

3. **Pair hover with focus-visible.** Every `:hover` style must have an equivalent `:focus-visible` style.

4. **No hover-only interactions.** Tooltips and dropdowns must work with `:focus-within` and click/tap. Touch devices have no hover state.

5. **Use CSS scroll snap for carousels.** `scroll-snap-type: x mandatory` on the container. `scroll-snap-align: start` on each item.

<!-- /SECTION: touch -->

---

<!-- SECTION: i18n -->
## Internationalization [MEDIUM]

### Rules

1. **Set `lang` attribute on `<html>`.** Use BCP 47 language tags (`en`, `fr`, `ar`, `zh-Hans`). Override with `lang` on elements containing different-language content.

2. **Use `dir="auto"` for user-generated content.** Let the browser detect text direction. Use `dir="rtl"` or `dir="ltr"` when direction is known.

3. **Format with Intl APIs.** `Intl.DateTimeFormat` for dates. `Intl.NumberFormat` for numbers and currency. `Intl.RelativeTimeFormat` for relative time. `Intl.ListFormat` for lists.

4. **Avoid text in images.** Text in images cannot be translated, resized, or read by screen readers.

5. **Use CSS logical properties.** `margin-inline-start` not `margin-left`. `padding-block-end` not `padding-bottom`. `inset-inline-start` not `left`. `text-align: start` not `text-align: left`.

6. **Support RTL layouts.** Test in RTL mode. Flip directional icons with `transform: scaleX(-1)` in `[dir="rtl"]`. Flexbox and Grid handle flow reversal automatically with logical properties.

<!-- /SECTION: i18n -->

---

## Quick Reference: Semantic HTML Elements

| Element | Use For | Replaces |
|---------|---------|----------|
| `<button>` | Actions, toggles | `<div onclick>`, `<a href="#">` |
| `<a href>` | Navigation to URLs | `<span onclick>` |
| `<nav>` | Navigation blocks | `<div class="nav">` |
| `<main>` | Primary content | `<div class="main">` |
| `<header>` | Page/section header | `<div class="header">` |
| `<footer>` | Page/section footer | `<div class="footer">` |
| `<article>` | Independent content | `<div class="article">` |
| `<section>` | Thematic group | `<div class="section">` |
| `<aside>` | Side content | `<div class="sidebar">` |
| `<dialog>` | Modal dialogs | `<div class="modal">` |
| `<details>/<summary>` | Disclosure | Custom accordion JS |
| `<fieldset>/<legend>` | Form groups | `<div class="group">` |
| `<figure>/<figcaption>` | Figures with captions | `<div class="image-wrap">` |
| `<time>` | Dates and times | `<span class="date">` |
| `<search>` | Search landmark | `<div role="search">` |
| `<output>` | Calculation result | `<span class="result">` |
| `<progress>` | Progress indicator | `<div class="progress">` |
| `<meter>` | Scalar measurement | `<div class="gauge">` |

## Quick Reference: Common ARIA Patterns

| Pattern | Key Attributes |
|---------|---------------|
| Tabs | `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected`, `aria-controls` |
| Accordion | `<button aria-expanded>`, `aria-controls`, `<div role="region">` |
| Modal | `<dialog>` or `role="dialog"`, `aria-modal="true"`, `aria-labelledby` |
| Combobox | `role="combobox"`, `aria-expanded`, `aria-controls`, `aria-activedescendant` |
| Alert | `role="alert"` (assertive) or `role="status"` (polite) |
| Tooltip | `role="tooltip"`, `aria-describedby` on trigger |
| Menu | `role="menu"`, `role="menuitem"`, `aria-haspopup` |
| Tree | `role="tree"`, `role="treeitem"`, `aria-expanded` |
| Breadcrumb | `<nav aria-label="Breadcrumb">`, `aria-current="page"` |
| Live region | `aria-live="polite"` or `aria-live="assertive"`, `aria-atomic` |
