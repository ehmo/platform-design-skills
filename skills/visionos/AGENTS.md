# visionOS Design Guidelines

Spatial computing design skill based on Apple Human Interface Guidelines for Apple Vision Pro.

## Purpose

Provide authoritative design guidance for visionOS apps covering spatial layout, eye and hand input, windows, volumes, immersive spaces, materials, and ornaments.

## Skill Files

| File | Contents |
|------|----------|
| `SKILL.md` | Complete design guidelines with rules, checklist, and anti-patterns |
| `rules/_sections.md` | Categorized rule reference with severity levels |
| `metadata.json` | Version, references, and abstract |

## Usage

This skill activates when tasks involve:
- visionOS or Apple Vision Pro development
- Spatial UI design and layout
- Eye and hand input implementation
- RealityKit or RealityComposer Pro
- Mixed reality or immersive experience design
- Window, volume, or immersive space architecture

## Rule Categories

| # | Category | Impact |
|---|----------|--------|
| 1 | Spatial Layout | CRITICAL |
| 2 | Eye & Hand Input | CRITICAL |
| 3 | Windows | HIGH |
| 4 | Volumes | HIGH |
| 5 | Immersive Spaces | HIGH |
| 6 | Materials & Depth | MEDIUM |
| 7 | Ornaments | MEDIUM |
| 8 | Accessibility | CRITICAL |

## Priority Levels

- **CRITICAL**: Violations break core spatial UX or cause user discomfort
- **HIGH**: Violations degrade experience significantly
- **MEDIUM**: Violations miss polish or platform conventions

## Never Do

- Never head-lock UI — all content must be world-anchored
- Never place interactive elements below 60pt in size
- Never skip hover/gaze feedback on interactive elements
- Never use gaze direction for analytics or content decisions (privacy violation)
- Never force immersion on launch — start in the Shared Space
- Never trap users in full immersion with no exit
- Never place content behind the user
- Never position windows or objects closer than ~0.5m (personal space)
- Never use opaque backgrounds in shared space (except media)
- Never omit accessibility labels on interactive elements and 3D objects
