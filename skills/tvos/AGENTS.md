# tvOS Design Guidelines

## Purpose

Provide Apple Human Interface Guidelines expertise for Apple TV app design. This skill covers focus-based navigation, Siri Remote interaction, 10-foot UI principles, Top Shelf extensions, media playback, and tab bar patterns.

## When to Use

- Designing or reviewing tvOS applications
- Building focus-based navigation systems
- Creating Top Shelf extensions
- Implementing media playback interfaces
- Adapting existing apps for the living room

## Key Principles

1. **Focus is everything** -- tvOS has no pointer or touch; all interaction is focus-driven
2. **Design for distance** -- assume 10-foot viewing; large text, bold imagery, high contrast
3. **Respect the remote** -- Siri Remote is simple; never require complex gestures
4. **Content first** -- TV is a lean-back, content-consumption experience
5. **Parallax communicates depth** -- use layered images to reinforce focus

## Rule Categories

| # | Category | Impact |
|---|----------|--------|
| 1 | Focus-Based Navigation | CRITICAL |
| 2 | Siri Remote | CRITICAL |
| 3 | 10-Foot UI | HIGH |
| 4 | Top Shelf | HIGH |
| 5 | Media & Playback | MEDIUM |
| 6 | Tab Bar | MEDIUM |
| 7 | Accessibility | CRITICAL |

## File Structure

- `SKILL.md` -- complete design rules and evaluation checklist
- `rules/_sections.md` -- categorized rules reference
- `metadata.json` -- version and source references

## Never Do

- Never use bottom tab bars — use the top tab bar
- Never trap focus — users must always be able to move away
- Never override the Menu button behavior
- Never require complex or multi-finger gestures on the Siri Remote
- Never repurpose the Play/Pause button for non-media actions
- Never use small touch targets — minimum 250x150pt for cards
- Never use body text below 29pt
- Never use TVTopShelfProvider (deprecated since tvOS 14) — use TVTopShelfContentProvider
- Never omit accessibility labels on image cards and icon buttons
- Never apply parallax animations without respecting Reduce Motion
