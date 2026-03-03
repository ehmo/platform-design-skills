---
name: web-design-guidelines
description: Web platform design and accessibility guidelines. Use when building web interfaces, auditing accessibility, implementing responsive layouts, or reviewing web UI code. Triggers on tasks involving HTML, CSS, web components, WCAG compliance, responsive design, or web performance.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# Web Platform Design Guidelines

Framework-agnostic rules for accessible, performant, responsive web interfaces. Based on WCAG 2.2, MDN Web Docs, and modern web platform APIs.

---

## 1. Accessibility / WCAG [CRITICAL]

Accessibility is not optional. Every rule in this section maps to WCAG 2.2 success criteria at Level A or AA.

### 1.1 Use Semantic HTML Elements

Use elements for their intended purpose. Semantic structure provides free accessibility, SEO, and reader-mode support.

| Element | Purpose |
|---------|---------|
| `<main>` | Primary page content (one per page) |
| `<nav>` | Navigation blocks |
| `<header>` | Introductory content or navigational aids |
| `<footer>` | Footer for nearest sectioning content |
| `<article>` | Self-contained, independently distributable content |
| `<section>` | Thematic grouping with a heading |
| `<aside>` | Tangentially related content (sidebars, callouts) |
| `<figure>` / `<figcaption>` | Illustrations, diagrams, code listings |
| `<details>` / `<summary>` | Expandable/collapsible disclosure widget |
| `<dialog>` | Modal or non-modal dialog boxes |
| `<time>` | Machine-readable dates/times |
| `<mark>` | Highlighted/referenced text |
| `<address>` | Contact information for nearest article/body |

```html
<!-- Good -->
<main>
  <article>
    <h1>Article Title</h1>
    <p>Content...</p>
  </article>
  <aside>Related links</aside>
</main>

<!-- Bad: div soup -->
<div class="main">
  <div class="article">
    <div class="title">Article Title</div>
    <div class="content">Content...</div>
  </div>
</div>
```

**Anti-pattern**: Using `<div>` or `<span>` for interactive elements. Never write `<div onclick>` when `<button>` exists.

### 1.2 ARIA Labels on Interactive Elements

Every interactive element must have an accessible name. Prefer visible text; use `aria-label` or `aria-labelledby` only when visible text is insufficient (SC 4.1.2).

```html
<!-- Icon-only button: needs aria-label -->
<button aria-label="Close dialog">
  <svg aria-hidden="true">...</svg>
</button>

<!-- Linked by labelledby -->
<h2 id="section-title">Notifications</h2>
<ul aria-labelledby="section-title">...</ul>

<!-- Redundant: visible text is enough -->
<button>Save Changes</button> <!-- No aria-label needed -->
```

### 1.3 Keyboard Navigation

All interactive elements must be reachable and operable via keyboard (SC 2.1.1).

- Use native interactive elements (`<button>`, `<a href>`, `<input>`, `<select>`) which are keyboard-accessible by default.
- Custom widgets need `tabindex="0"` to enter tab order and keydown handlers for activation.
- Never use `tabindex` values greater than 0.
- Trap focus inside modals; return focus on close.

```js
// Focus trap for modal
dialog.addEventListener('keydown', (e) => {
  if (e.key === 'Tab') {
    const focusable = dialog.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    const first = focusable[0];
    const last = focusable[focusable.length - 1];
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  }
});
```

### 1.4 Visible Focus Indicators

Never remove focus outlines without providing a visible replacement (SC 2.4.7, enhanced 2.4.11/2.4.12 in WCAG 2.2).

```css
/* Good: custom focus indicator */
:focus-visible {
  outline: 3px solid var(--focus-color, #4A90D9);
  outline-offset: 2px;
}

/* Remove default only when :focus-visible is supported */
:focus:not(:focus-visible) {
  outline: none;
}

/* Bad: removing all focus styles */
/* *:focus { outline: none; } */
```

WCAG 2.2 requires focus indicators to have a minimum area of the perimeter of the component times 2px, with 3:1 contrast against adjacent colors.

### 1.5 Skip Navigation Links

Provide a mechanism to skip repeated blocks of content (SC 2.4.1).

```html
<body>
  <a href="#main-content" class="skip-link">Skip to main content</a>
  <nav>...</nav>
  <main id="main-content">...</main>
</body>
```

```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  z-index: 1000;
  padding: 0.75rem 1.5rem;
  background: var(--color-primary);
  color: var(--color-on-primary);
}
.skip-link:focus {
  top: 0;
}
```

### 1.6 Alt Text for Images

Every `<img>` must have an `alt` attribute (SC 1.1.1).

- **Informative images**: describe the content and function. `alt="Bar chart showing sales doubled in Q4"`.
- **Decorative images**: use `alt=""` (empty string) so screen readers skip them.
- **Functional images** (inside links/buttons): describe the action. `alt="Search"`.
- **Complex images**: use `alt` for short description, link to long description or use `<figcaption>`.

```html
<img src="chart.png" alt="Revenue chart: Q1 $2M, Q2 $2.4M, Q3 $3.1M, Q4 $4.5M">
<img src="decorative-wave.svg" alt="">
```

### 1.7 Color Contrast

Maintain minimum contrast ratios (SC 1.4.3, 1.4.6, 1.4.11).

| Content | Minimum Ratio |
|---------|--------------|
| Normal text (<24px / <18.66px bold) | 4.5:1 |
| Large text (>=24px / >=18.66px bold) | 3:1 |
| UI components and graphical objects | 3:1 |

Do not rely on color alone to convey information (SC 1.4.1). Pair color with icons, text, or patterns.

```css
/* Check contrast of these tokens */
:root {
  --text-primary: #1a1a2e;    /* on white: ~16:1 */
  --text-secondary: #555770;  /* on white: ~6.5:1 */
  --text-disabled: #767693;   /* on white: ~4.5:1, borderline */
}
```

### 1.8 Form Labels

Every form input must have a programmatically associated label (SC 1.3.1, 3.3.2).

```html
<!-- Explicit label (preferred) -->
<label for="email">Email address</label>
<input id="email" type="email" autocomplete="email">

<!-- Implicit label (acceptable) -->
<label>
  Email address
  <input type="email" autocomplete="email">
</label>

<!-- Never: placeholder as sole label -->
<!-- <input placeholder="Email"> -->
```

### 1.9 Error Identification

Identify and describe errors in text (SC 3.3.1). Link error messages to inputs with `aria-describedby` or `aria-errormessage`.

```html
<label for="email">Email</label>
<input id="email" type="email" aria-describedby="email-error" aria-invalid="true">
<p id="email-error" role="alert">Enter a valid email address, e.g. name@example.com</p>
```

### 1.10 ARIA Live Regions

Announce dynamic content changes to screen readers (SC 4.1.3).

```html
<!-- Polite: announced when user is idle -->
<div aria-live="polite" aria-atomic="true">
  3 results found
</div>

<!-- Assertive: interrupts current speech -->
<div role="alert">
  Your session will expire in 2 minutes.
</div>

<!-- Status messages -->
<div role="status">
  File uploaded successfully.
</div>
```

Use `aria-live="polite"` by default. Reserve `role="alert"` / `aria-live="assertive"` for time-sensitive warnings.

### 1.11 ARIA Role Quick Reference

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

## 2. Responsive Design [CRITICAL]

### 2.1 Mobile-First Approach

Write base styles for the smallest viewport. Layer complexity with `min-width` media queries.

```css
/* Base: mobile */
.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

/* Tablet */
@media (min-width: 48rem) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

/* Desktop */
@media (min-width: 64rem) {
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### 2.2 Fluid Layouts with Modern CSS Functions

Use `clamp()`, `min()`, and `max()` for fluid sizing without breakpoints.

```css
/* Fluid typography */
h1 {
  font-size: clamp(1.75rem, 1.2rem + 2vw, 3rem);
}

/* Fluid spacing */
.section {
  padding: clamp(1.5rem, 4vw, 4rem);
}

/* Fluid container */
.container {
  width: min(90%, 72rem);
  margin-inline: auto;
}
```

### 2.3 Container Queries

Size components based on their container, not the viewport.

```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 200px 1fr;
  }
}

@container card (min-width: 700px) {
  .card {
    grid-template-columns: 300px 1fr;
    gap: 2rem;
  }
}
```

### 2.4 Content-Based Breakpoints

Set breakpoints where your content breaks, not at device widths. Common starting points:

```css
/* Content-based, not "iPhone" or "iPad" */
@media (min-width: 30rem)  { /* ~480px: single column gets cramped */ }
@media (min-width: 48rem)  { /* ~768px: room for 2 columns */ }
@media (min-width: 64rem)  { /* ~1024px: room for sidebar + content */ }
@media (min-width: 80rem)  { /* ~1280px: wide multi-column */ }
```

### 2.5 Touch Targets

Minimum 44x44 CSS pixels for touch targets (WCAG SC 2.5.8). Provide at least 24px spacing between adjacent targets.

```css
button, a, input, select, textarea {
  min-height: 44px;
  min-width: 44px;
}

/* Enlarge tap area without changing visual size */
.icon-button {
  position: relative;
  width: 24px;
  height: 24px;
}
.icon-button::after {
  content: "";
  position: absolute;
  inset: -10px; /* expands clickable area */
}
```

### 2.6 Viewport Meta Tag

Always include in the document `<head>`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

Never use `maximum-scale=1` or `user-scalable=no` -- these break pinch-to-zoom accessibility (SC 1.4.4).

### 2.7 No Horizontal Scrolling

Content must reflow at 320px width without horizontal scrolling (SC 1.4.10).

```css
/* Prevent overflow */
img, video, iframe, svg {
  max-width: 100%;
  height: auto;
}

/* Contain long words/URLs */
.prose {
  overflow-wrap: break-word;
}

/* Tables: scroll container, not page */
.table-wrapper {
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}
```

---

## 3. Forms [HIGH]

### 3.1 Label Every Input

Every input needs a visible, programmatically associated label. See section 1.8.

### 3.2 Autocomplete Attributes

Use `autocomplete` for common fields to enable browser autofill (SC 1.3.5).

```html
<input type="text" autocomplete="name" name="full-name">
<input type="email" autocomplete="email" name="email">
<input type="tel" autocomplete="tel" name="phone">
<input type="text" autocomplete="street-address" name="address">
<input type="text" autocomplete="postal-code" name="zip">
<input type="text" autocomplete="cc-name" name="card-name">
<input type="text" autocomplete="cc-number" name="card-number">
<input type="password" autocomplete="new-password" name="password">
<input type="password" autocomplete="current-password" name="current-pw">
```

### 3.3 Correct Input Types

Use the right `type` to trigger appropriate mobile keyboards and native validation.

| Type | Use For |
|------|---------|
| `email` | Email addresses |
| `tel` | Phone numbers |
| `url` | URLs |
| `number` | Numeric values with spinners (not for phone, zip, card numbers) |
| `search` | Search fields (shows clear button) |
| `date` / `time` / `datetime-local` | Temporal values |
| `password` | Passwords (triggers password manager) |
| `text` with `inputmode="numeric"` | Numeric data without spinners (PINs, zip codes) |

```html
<input type="tel" inputmode="numeric" pattern="[0-9]*" autocomplete="one-time-code">
```

### 3.4 Inline Validation

Validate on `blur` (not on every keystroke). Show success and error states.

```html
<div class="field" data-state="error">
  <label for="username">Username</label>
  <input id="username" type="text" aria-describedby="username-hint username-error" aria-invalid="true">
  <p id="username-hint" class="hint">3-20 characters, letters and numbers only</p>
  <p id="username-error" class="error" role="alert">Username must be at least 3 characters</p>
</div>
```

```css
.field[data-state="error"] input {
  border-color: var(--color-error);
  box-shadow: 0 0 0 1px var(--color-error);
}
.field[data-state="error"] .error { display: block; }
.field:not([data-state="error"]) .error { display: none; }
```

### 3.5 Fieldset and Legend for Groups

Group related inputs with `<fieldset>` and label the group with `<legend>`.

```html
<fieldset>
  <legend>Shipping Address</legend>
  <label for="street">Street</label>
  <input id="street" type="text" autocomplete="street-address">
  <!-- ... -->
</fieldset>

<fieldset>
  <legend>Preferred contact method</legend>
  <label><input type="radio" name="contact" value="email"> Email</label>
  <label><input type="radio" name="contact" value="phone"> Phone</label>
</fieldset>
```

### 3.6 Required Field Indication

Indicate required fields visually and programmatically. Use `required` attribute and visible markers.

```html
<label for="name">
  Full name <span aria-hidden="true">*</span>
  <span class="sr-only">(required)</span>
</label>
<input id="name" type="text" required autocomplete="name">
```

If most fields are required, indicate which are optional instead.

### 3.7 Submit Button State

Do not disable the submit button. Instead, validate on submit and show errors.

```html
<!-- Good: always enabled, validate on submit -->
<button type="submit">Create Account</button>

<!-- Bad: disabled button with no explanation -->
<!-- <button type="submit" disabled>Create Account</button> -->
```

Disabled buttons fail to communicate why the user cannot proceed. If you must disable, provide a visible explanation.

---

## 4. Typography [HIGH]

### 4.1 Font Stacks

Use system font stacks for performance, or web fonts with proper fallbacks.

```css
/* System font stack */
body {
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
}

/* Monospace stack */
code, pre, kbd {
  font-family: ui-monospace, "Cascadia Code", "Source Code Pro", Menlo, Consolas, monospace;
}

/* Web font with fallbacks and size-adjust */
@font-face {
  font-family: "CustomFont";
  src: url("/fonts/custom.woff2") format("woff2");
  font-display: swap;
  font-weight: 100 900;
}
body {
  font-family: "CustomFont", system-ui, sans-serif;
}
```

### 4.2 Relative Units

Use `rem` for font sizes and spacing. Use `em` for component-relative sizing.

```css
html {
  font-size: 100%; /* = 16px default, respects user preference */
}

body {
  font-size: 1rem;       /* 16px */
}

h1 { font-size: 2.5rem; }  /* 40px */
h2 { font-size: 2rem; }    /* 32px */
h3 { font-size: 1.5rem; }  /* 24px */
small { font-size: 0.875rem; } /* 14px */

/* Never: font-size: 16px; (ignores user zoom settings) */
```

### 4.3 Line Height and Spacing

Body text line height of at least 1.5 (SC 1.4.12). Paragraph spacing at least 2x font size.

```css
body {
  line-height: 1.6;
}

h1, h2, h3 {
  line-height: 1.2;
}

p + p {
  margin-top: 1em;
}
```

### 4.4 Maximum Line Length

Limit line length to approximately 75 characters for readability.

```css
.prose {
  max-width: 75ch;
}

/* Or for a content column */
.content {
  max-width: 40rem; /* roughly 65-75ch depending on font */
  margin-inline: auto;
}
```

### 4.5 Typographic Details

Use real quotes, proper dashes, and tabular numbers for data.

```css
/* Smart quotes */
q { quotes: "\201C" "\201D" "\2018" "\2019"; } /* curly double then single */

/* Tabular numbers for aligned data */
.data-table td {
  font-variant-numeric: tabular-nums;
}

/* Oldstyle numbers for running prose (optional) */
.prose {
  font-variant-numeric: oldstyle-nums;
}

/* Proper list markers */
ul { list-style-type: disc; }
ol { list-style-type: decimal; }
```

### 4.6 Heading Hierarchy

Use `h1` through `h6` in order. Never skip levels. One `h1` per page.

```html
<!-- Good -->
<h1>Page Title</h1>
  <h2>Section</h2>
    <h3>Subsection</h3>
  <h2>Another Section</h2>

<!-- Bad: skipping h2 -->
<h1>Page Title</h1>
  <h3>Subsection</h3> <!-- Where is h2? -->
```

If you need visual styling that differs from the hierarchy, use CSS classes:

```html
<h2 class="text-lg">Visually smaller but semantically h2</h2>
```

---

## 5. Performance [HIGH]

### 5.1 Lazy Load Below-Fold Images

Use native lazy loading for images not visible on initial load.

```html
<!-- Above fold: load eagerly, add fetchpriority -->
<img src="hero.webp" alt="Hero image" fetchpriority="high" width="1200" height="600">

<!-- Below fold: lazy load -->
<img src="feature.webp" alt="Feature image" loading="lazy" width="600" height="400">
```

### 5.2 Explicit Image Dimensions

Always specify `width` and `height` to prevent layout shift (CLS).

```html
<img src="photo.webp" alt="Description" width="800" height="600">
```

```css
/* Responsive images with aspect ratio preservation */
img {
  max-width: 100%;
  height: auto;
}
```

### 5.3 Resource Hints

Use `preconnect` for third-party origins and `preload` for critical resources.

```html
<head>
  <!-- Preconnect to critical third-party origins -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://cdn.example.com" crossorigin>

  <!-- Preload critical resources -->
  <link rel="preload" href="/fonts/main.woff2" as="font" type="font/woff2" crossorigin>
  <link rel="preload" href="/css/critical.css" as="style">

  <!-- DNS prefetch for non-critical origins -->
  <link rel="dns-prefetch" href="https://analytics.example.com">
</head>
```

### 5.4 Code Splitting

Load JavaScript only when needed. Use dynamic `import()` for route-based and component-based splitting.

```js
// Route-based splitting
const routes = {
  '/dashboard': () => import('./pages/dashboard.js'),
  '/settings':  () => import('./pages/settings.js'),
};

// Interaction-based splitting
button.addEventListener('click', async () => {
  const { openEditor } = await import('./editor.js');
  openEditor();
});
```

### 5.5 Virtualize Long Lists

For lists exceeding a few hundred items, render only visible rows.

```js
// Concept: virtual scrolling
// Render only items in viewport + buffer
const visibleStart = Math.floor(scrollTop / itemHeight);
const visibleEnd = visibleStart + Math.ceil(containerHeight / itemHeight);
const buffer = 5;
const renderStart = Math.max(0, visibleStart - buffer);
const renderEnd = Math.min(totalItems, visibleEnd + buffer);
```

### 5.6 Avoid Layout Thrashing

Batch DOM reads and writes. Never interleave them.

```js
// Bad: read-write-read-write (forces synchronous layout)
elements.forEach(el => {
  const height = el.offsetHeight;     // read
  el.style.height = height + 10 + 'px'; // write
});

// Good: batch reads, then batch writes
const heights = elements.map(el => el.offsetHeight); // all reads
elements.forEach((el, i) => {
  el.style.height = heights[i] + 10 + 'px'; // all writes
});
```

### 5.7 Use `will-change` Sparingly

Only apply `will-change` to elements that will animate, and remove it after animation completes.

```css
/* Good: scoped and temporary */
.card:hover {
  will-change: transform;
}
.card.animating {
  will-change: transform, opacity;
}

/* Bad: blanket will-change */
/* * { will-change: transform; } */
```

---

## 6. Animation and Motion [MEDIUM]

### 6.1 Respect prefers-reduced-motion

Always provide a reduced-motion alternative (SC 2.3.3).

```css
/* Define animations normally */
.fade-in {
  animation: fadeIn 300ms ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Remove or reduce for users who prefer it */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

```js
// Check in JavaScript
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
```

### 6.2 Compositor-Friendly Animations

Animate only `transform` and `opacity` for smooth 60fps animation. These run on the GPU compositor thread.

```css
/* Good: compositor-only properties */
.slide-in {
  transition: transform 200ms ease-out, opacity 200ms ease-out;
}

/* Bad: triggers layout/paint */
.slide-in-bad {
  transition: left 200ms, width 200ms, height 200ms;
}
```

### 6.3 No Flashing Content

Never flash content more than 3 times per second (SC 2.3.1). This can trigger seizures.

### 6.4 Transitions for State Changes

Use transitions for hover, focus, open/close, and other state changes to provide continuity.

```css
.dropdown {
  opacity: 0;
  transform: translateY(-4px);
  transition: opacity 150ms ease-out, transform 150ms ease-out;
  pointer-events: none;
}
.dropdown.open {
  opacity: 1;
  transform: translateY(0);
  pointer-events: auto;
}
```

### 6.5 Meaningful Motion Only

Animation should communicate state, guide attention, or show spatial relationships. Never animate for decoration alone.

---

## 7. Dark Mode and Theming [MEDIUM]

### 7.1 System Preference Detection

```css
@media (prefers-color-scheme: dark) {
  :root {
    --bg: #0f0f17;
    --text: #e4e4ef;
    --surface: #1c1c2e;
    --border: #2e2e44;
  }
}
```

### 7.2 CSS Custom Properties for Theming

Define all theme values as custom properties. Toggle themes by changing property values.

```css
:root {
  color-scheme: light dark;

  /* Light theme (default) */
  --color-bg: #ffffff;
  --color-surface: #f5f5f7;
  --color-text-primary: #1a1a2e;
  --color-text-secondary: #555770;
  --color-border: #d1d1e0;
  --color-primary: #2563eb;
  --color-primary-text: #ffffff;
  --color-error: #dc2626;
  --color-success: #16a34a;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #0f0f17;
    --color-surface: #1c1c2e;
    --color-text-primary: #e4e4ef;
    --color-text-secondary: #a0a0b8;
    --color-border: #2e2e44;
    --color-primary: #60a5fa;
    --color-primary-text: #0f0f17;
    --color-error: #f87171;
    --color-success: #4ade80;
  }
}
```

### 7.3 Color-Scheme Meta Tag

Tell the browser about supported color schemes for native UI elements (scrollbars, form controls).

```html
<meta name="color-scheme" content="light dark">
```

### 7.4 Maintain Contrast in Both Modes

Verify contrast ratios in both light and dark modes. Dark mode often suffers from low-contrast text on dark surfaces.

### 7.5 Adaptive Images

Provide appropriate images for light and dark contexts.

```html
<picture>
  <source srcset="logo-dark.svg" media="(prefers-color-scheme: dark)">
  <img src="logo-light.svg" alt="Company logo">
</picture>
```

```css
/* Or use CSS filter for simple cases */
@media (prefers-color-scheme: dark) {
  .decorative-img {
    filter: brightness(0.9) contrast(1.1);
  }
}
```

---

## 8. Navigation and State [MEDIUM]

### 8.1 URL Reflects State

Every meaningful view should have a unique URL. Users should be able to bookmark, share, and reload any state.

```js
// Update URL without full page reload
function updateFilters(filters) {
  const params = new URLSearchParams(filters);
  history.pushState(null, '', `?${params}`);
  renderResults(filters);
}

// Restore state from URL on load
const params = new URLSearchParams(location.search);
const initialFilters = Object.fromEntries(params);
```

### 8.2 Browser Back/Forward

Handle `popstate` to support browser navigation.

```js
window.addEventListener('popstate', () => {
  const params = new URLSearchParams(location.search);
  renderResults(Object.fromEntries(params));
});
```

### 8.3 Active Navigation States

Indicate the current page or section in navigation. Use `aria-current="page"` for the active link.

```html
<nav aria-label="Main">
  <a href="/" aria-current="page">Home</a>
  <a href="/products">Products</a>
  <a href="/about">About</a>
</nav>
```

```css
[aria-current="page"] {
  font-weight: 700;
  border-bottom: 2px solid var(--color-primary);
}
```

### 8.4 Breadcrumbs

Provide breadcrumbs for sites with deep hierarchies.

```html
<nav aria-label="Breadcrumb">
  <ol>
    <li><a href="/">Home</a></li>
    <li><a href="/products">Products</a></li>
    <li><a href="/products/widgets" aria-current="page">Widgets</a></li>
  </ol>
</nav>
```

### 8.5 Scroll Restoration

Manage scroll position for SPA navigation.

```js
// Disable browser auto-restoration for manual control
if ('scrollRestoration' in history) {
  history.scrollRestoration = 'manual';
}

// Save scroll position before navigation
function saveScrollPosition() {
  sessionStorage.setItem(`scroll-${location.pathname}`, window.scrollY);
}

// Restore on back/forward
window.addEventListener('popstate', () => {
  const saved = sessionStorage.getItem(`scroll-${location.pathname}`);
  if (saved) {
    requestAnimationFrame(() => window.scrollTo(0, parseInt(saved)));
  }
});
```

---

## 9. Touch and Interaction [MEDIUM]

### 9.1 Touch-Action for Scroll Control

Use `touch-action` to control gesture behavior on interactive elements.

```css
/* Allow only vertical scrolling (disable horizontal pan and pinch-zoom) */
.vertical-scroll {
  touch-action: pan-y;
}

/* Carousel: horizontal scroll only */
.carousel {
  touch-action: pan-x;
}

/* Canvas/map: disable all browser gestures */
.canvas {
  touch-action: none;
}
```

### 9.2 Tap Highlight

Control the tap highlight on mobile WebKit browsers.

```css
button, a {
  -webkit-tap-highlight-color: transparent;
}
```

### 9.3 Hover and Focus Parity

Every hover interaction must also work with keyboard focus.

```css
/* Always pair :hover with :focus-visible */
.card:hover,
.card:focus-visible {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transform: translateY(-2px);
}
```

### 9.4 No Hover-Only Interactions

Never hide essential functionality behind hover. Touch devices have no hover state.

```css
/* Bad: content only accessible on hover */
/* .tooltip { display: none; }
   .trigger:hover .tooltip { display: block; } */

/* Good: works with focus and click too */
.trigger:hover .tooltip,
.trigger:focus-within .tooltip,
.tooltip:focus-within {
  display: block;
}
```

### 9.5 Scroll Snap for Carousels

Use CSS scroll snap for card carousels and horizontal lists.

```css
.carousel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  gap: 1rem;
  scroll-padding: 1rem;
}

.carousel > .slide {
  scroll-snap-align: start;
  flex: 0 0 min(85%, 400px);
}
```

---

## 10. Internationalization [MEDIUM]

### 10.1 dir and lang Attributes

Set `lang` on the `<html>` element. Use `dir="auto"` for user-generated content.

```html
<html lang="en" dir="ltr">

<!-- User-generated content: let browser detect direction -->
<p dir="auto">User-submitted text here</p>

<!-- Explicit override for known RTL content -->
<blockquote lang="ar" dir="rtl">...</blockquote>
```

### 10.2 Intl APIs for Formatting

Use the `Intl` API for locale-aware formatting. Never hard-code date or number formats.

```js
// Dates
new Intl.DateTimeFormat('en-US', { dateStyle: 'long' }).format(date);
// "January 15, 2026"

// Numbers
new Intl.NumberFormat('de-DE', { style: 'currency', currency: 'EUR' }).format(1234.56);
// "1.234,56 EUR"

// Relative time
new Intl.RelativeTimeFormat('en', { numeric: 'auto' }).format(-1, 'day');
// "yesterday"

// Lists
new Intl.ListFormat('en', { style: 'long', type: 'conjunction' }).format(['a', 'b', 'c']);
// "a, b, and c"

// Plurals
const pr = new Intl.PluralRules('en');
const suffixes = { one: 'st', two: 'nd', few: 'rd', other: 'th' };
function ordinal(n) { return `${n}${suffixes[pr.select(n)]}`; }
```

### 10.3 Avoid Text in Images

Text in images cannot be translated, resized, or read by screen readers. Use HTML/CSS text with background images when a styled text overlay is needed.

### 10.4 CSS Logical Properties

Use logical properties instead of physical ones to support both LTR and RTL layouts.

```css
/* Physical (breaks in RTL) */
/* margin-left: 1rem; padding-right: 2rem; border-left: 1px solid; */

/* Logical (works in LTR and RTL) */
.sidebar {
  margin-inline-start: 1rem;
  padding-inline-end: 2rem;
  border-inline-start: 1px solid var(--color-border);
}

.stack > * + * {
  margin-block-start: 1rem;
}

/* Logical shorthands */
.box {
  margin-inline: auto;     /* left + right */
  padding-block: 2rem;     /* top + bottom */
  inset-inline-start: 0;   /* left in LTR, right in RTL */
  border-start-start-radius: 8px; /* top-left in LTR, top-right in RTL */
}
```

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

### 10.5 RTL Layout Support

Test layouts in RTL mode. Flexbox and Grid handle RTL automatically with logical properties.

```css
/* This layout works in both LTR and RTL without changes */
.layout {
  display: flex;
  gap: 1rem;
}

/* Icons that indicate direction need flipping */
[dir="rtl"] .arrow-icon {
  transform: scaleX(-1);
}
```

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
