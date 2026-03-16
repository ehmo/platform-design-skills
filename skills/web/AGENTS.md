# Web Platform Design Skills

## Purpose

Framework-agnostic web design and accessibility guidelines based on WCAG 2.2, MDN Web Docs, and modern web platform standards.

## File Structure

```
web/
  metadata.json        # Version, references, abstract
  SKILL.md             # Full guidelines (load this)
  AGENTS.md            # This file
  rules/
    _sections.md       # Sectioned rules for selective loading
```

## Usage

- Load `SKILL.md` for complete guidelines when building or reviewing web UI.
- Load `rules/_sections.md` for individual category rules when working on a specific concern (e.g., only accessibility, only forms).

## When to Apply

- Building HTML/CSS/JS interfaces
- Auditing accessibility or WCAG compliance
- Implementing responsive layouts
- Reviewing web UI pull requests
- Optimizing web performance
- Adding dark mode or theming
- Internationalizing web content

## Priority Levels

| Level | Categories |
|-------|-----------|
| CRITICAL | Accessibility/WCAG, Responsive Design |
| HIGH | Forms, Typography, Performance |
| MEDIUM | Animation, Dark Mode, Navigation, Touch, i18n, Progressive Web Apps |

## Conventions

- Rules use imperative voice ("Use semantic HTML", not "You should use").
- Code examples are vanilla HTML/CSS/JS unless noted.
- WCAG success criteria referenced as (SC x.x.x).
- Priority in brackets: [CRITICAL], [HIGH], [MEDIUM].

## Never Do

- Never use `<div onclick>` when `<button>` exists — use semantic HTML
- Never set `maximum-scale=1` or `user-scalable=no` in the viewport meta tag
- Never rely on color alone to convey information — pair with text or icons
- Never use placeholder text as the only label for a form input
- Never build interactive elements that are only keyboard-accessible via `tabindex > 0`
- Never omit `alt` attributes on images — use `alt=""` for decorative images
- Never animate content that flashes more than 3 times per second (seizure risk)
- Never trap keyboard focus inside a component without providing a documented escape
- Never use `<table>` for layout — tables are for tabular data only
- Never set `font-size` in `px` on body text — use `rem` so user preferences apply
