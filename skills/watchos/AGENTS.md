# watchOS Design Guidelines

## Purpose

This skill provides Apple Human Interface Guidelines for Apple Watch. Load it when working on watchOS apps, complications, workout features, or any wrist-based interaction design.

## When to Use

- Building or reviewing watchOS app interfaces
- Designing complications for watch faces
- Implementing Digital Crown interactions
- Creating workout or health tracking features
- Designing notifications for Apple Watch
- Evaluating glanceability of Watch UI
- Working with Always On display states

## File Structure

| File | Contents |
|------|----------|
| `SKILL.md` | Complete design rules, specifications, and evaluation checklist |
| `rules/_sections.md` | All rules organized by category with IDs for cross-reference |
| `metadata.json` | Version, references, and abstract |

## Key Principles

1. **Glanceable first** -- every screen must communicate its purpose within 2 seconds
2. **Respect the wrist** -- interactions should be brief; avoid complex multi-step flows
3. **Digital Crown is primary** -- scroll, select, and adjust values with the Crown
4. **Complications drive engagement** -- keep watch face data fresh and useful
5. **Always On awareness** -- dim gracefully, hide sensitive content

## Priority Reference

| Level | Meaning |
|-------|---------|
| CRITICAL | Glanceable design -- violations make the app unusable on Watch |
| HIGH | Digital Crown, navigation, complications -- core Watch interaction patterns |
| MEDIUM | Always On, workouts, notifications -- important but context-dependent |
