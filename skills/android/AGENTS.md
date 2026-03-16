# Android Design Skill

Material Design 3 guidelines and Android platform conventions for Jetpack Compose and XML layouts.

## Structure

- `SKILL.md` — Full guideline rules with code examples
- `rules/_sections.md` — Indexed section breakdown for quick lookup
- `metadata.json` — Version and reference metadata

## Usage

Apply these guidelines when:
- Building or reviewing Android UI code
- Implementing Material You / dynamic color
- Designing navigation, layout, or component architecture
- Auditing accessibility or platform compliance

## Priority

Rules marked **CRITICAL** must never be violated. Rules marked **HIGH** should be followed unless there is a documented reason. Rules marked **MEDIUM** are recommended best practices.

## Never Do

- Never hardcode color hex values — always use `MaterialTheme.colorScheme` color roles
- Never use `dp` for text sizes — use `sp` so user font scaling applies
- Never override `onBackPressed()` — use `BackHandler` (Compose) or `OnBackInvokedCallback` (View-based) for predictive back
- Never place touch targets below 48x48dp — accessibility violation
- Never request permissions at app launch — request in context with a rationale
- Never use pure black (#000000) for dark theme backgrounds — use Material surface roles
- Never put icon-only items in the navigation bar — labels are required
- Never use a dialog for non-critical information — prefer Snackbar or Bottom Sheet
- Never use more than one FAB on a screen — one FAB for the single primary action
- Never show full-width content on tablet layouts — use list-detail or max-width containers
