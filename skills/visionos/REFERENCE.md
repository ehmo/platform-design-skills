# visionOS Reference Tables

## Spatial Interaction Quick Reference

| Interaction | Method | Use Case |
|---|---|---|
| Tap / Select | Look + pinch | Buttons, links, list items |
| Scroll | Look + pinch-and-drag | Lists, long content |
| Zoom | Two-hand pinch | Maps, images, 3D models |
| Rotate | Two-hand twist | 3D object manipulation |
| Drag | Look + pinch-hold-and-move | Repositioning windows |
| Direct touch | Reach and tap | Close-range 3D objects |
| Long press | Look + pinch-and-hold | Context menus |

## Target Size Reference

| Element | Minimum Size | Recommended Size |
|---|---|---|
| Buttons | 60pt | 60-80pt |
| List rows | 60pt height | 80pt height |
| Tab bar items | 60pt | 72pt |
| Close/dismiss | 60pt | 60pt |
| Toolbar items | 60pt | 60pt |
| 3D interactive objects | 60pt equivalent | Scale to context |

## Anti-Patterns

| Anti-Pattern | Problem | Correct Approach |
|---|---|---|
| Head-locked UI | Causes motion sickness, feels claustrophobic | Anchor UI to world space |
| Tiny tap targets | Eye tracking cannot reliably target < 60pt | Minimum 60pt interactive targets |
| No hover states | Users cannot tell what is interactive | Always show highlight on gaze |
| Opaque windows in shared space | Creates visual holes in environment | Use system glass material |
| Forced full immersion | Disorienting, traps users | Start in shared space, progressive immersion |
| Content behind user | Invisible, requires full body rotation | Keep content in forward hemisphere |
| Gaze-driven content | Privacy violation, feels surveilled | Use gaze only for system targeting |
| Flat 3D volumes | Looks like cardboard cutout from side | Design for all viewing angles |
| Inline toolbars | Wastes content space, breaks conventions | Use ornaments for controls |
| Small room assumptions | Fails in tight spaces | Adapt to available physical space |
| Abrupt immersion changes | Disorienting, breaks presence | Gradual transitions with animation |
| Sustained arm raising | Physical fatigue in minutes | Design for hands resting at sides |
| Custom window chrome | Breaks platform consistency | Use system window controls |
| Z-fighting layers | Visual flicker, unclear hierarchy | Use intentional depth offsets |
