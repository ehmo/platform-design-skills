# visionOS Design Rules — Categorized Reference

Quick-reference rule index organized by category and severity.

---

## CRITICAL

Rules where violations break core spatial UX or cause user discomfort.

### Spatial Layout

| ID | Rule | Summary |
|---|---|---|
| SL-01 | Center content in field of view | Primary content at eye level, directly ahead |
| SL-02 | Comfortable distance | Windows at 1-2m; default ~1.5m |
| SL-03 | No content behind user | All UI in forward hemisphere |
| SL-04 | Respect personal space | Nothing closer than ~0.5m to head |
| SL-07 | World-anchored, not head-locked | Content fixed in space, not following head |

### Eye & Hand Input

| ID | Rule | Summary |
|---|---|---|
| EH-01 | Look-and-pinch primary interaction | Eyes target, fingers confirm |
| EH-02 | 60pt minimum target size | All interactive elements >= 60pt |
| EH-03 | Hover feedback on gaze | Visible highlight when eye rests on element |
| EH-05 | No gaze tracking for content | Eye data is system-only, never for analytics |
| EH-07 | No precise hand positioning | Users interact with hands at rest |

---

## HIGH

Rules where violations significantly degrade the experience.

### Spatial Layout

| ID | Rule | Summary |
|---|---|---|
| SL-05 | Z-depth for hierarchy | Closer = more prominent; subtle offsets |
| SL-06 | Manage multiple windows | Arc arrangement, individually repositionable |

### Eye & Hand Input

| ID | Rule | Summary |
|---|---|---|
| EH-04 | Direct touch for close range | Physical tap on objects within reach |
| EH-06 | Simple custom gestures | Easy to discover, comfortable, no conflicts |

### Windows

| ID | Rule | Summary |
|---|---|---|
| WN-01 | Glass material background | System glass adapts to environment |
| WN-02 | Standard window controls | System window bar and close button |
| WN-03 | Resizable windows | Support resize with responsive layout |
| WN-04 | Tab bar as leading ornament | Vertical nav on left edge |
| WN-05 | Toolbar as bottom ornament | Primary actions along bottom edge |
| WN-06 | Windows float in space | No fixed screen assumptions |

### Volumes

| ID | Rule | Summary |
|---|---|---|
| VL-01 | Content within bounds | No clipping; size volume to content |
| VL-02 | Viewable from all angles | No flat facades |
| VL-03 | No required viewing position | Comprehensible from any angle |
| VL-04 | Appropriate scale | Size relative to content and environment |
| VL-05 | Volumes for 3D-primary experiences | Not windows with 3D inside |

### Immersive Spaces

| ID | Rule | Summary |
|---|---|---|
| IS-01 | Start in Shared Space | Default to windows alongside reality |
| IS-02 | Progressive immersion | Shared Space -> Full Space -> Full Immersion |
| IS-03 | Always provide exit path | Digital Crown + in-app controls |
| IS-04 | Passthrough for safety | Maintain awareness of physical environment |
| IS-05 | Gradual passthrough dimming | Smooth transitions, no hard cuts |
| IS-06 | No room assumptions | Adapt to available physical space |
| IS-07 | Spatial audio cues | Sound from source direction |

---

## MEDIUM

Rules where violations miss platform polish or conventions.

### Materials & Depth

| ID | Rule | Summary |
|---|---|---|
| MD-01 | System glass material | Use provided glass variants |
| MD-02 | Environment-responsive highlights | Materials react to real-world light |
| MD-03 | Layered materials for hierarchy | Lighter/thicker glass = foreground |
| MD-04 | Vibrancy for text | System text styles with vibrancy |
| MD-05 | Shadows and highlights for elevation | Depth cues on floating elements |
| MD-06 | No opaque backgrounds in shared space | Translucent glass; opaque only for media |

### Ornaments

| ID | Rule | Summary |
|---|---|---|
| OR-01 | Controls as ornaments | Not inline within content |
| OR-02 | Primary actions bottom | Bottom ornament for main controls |
| OR-03 | Navigation on leading edge | Tab bar on left side |
| OR-04 | No content occlusion | Ornaments extend outward |
| OR-05 | Contextual visibility | Show/hide on hover when appropriate |
| OR-06 | Standard ornament styling | System glass containers |

---

## Rule Count Summary

| Category | Count | Severity |
|---|---|---|
| Spatial Layout | 7 | 5 CRITICAL, 2 HIGH |
| Eye & Hand Input | 7 | 5 CRITICAL, 2 HIGH |
| Windows | 6 | 6 HIGH |
| Volumes | 5 | 5 HIGH |
| Immersive Spaces | 7 | 7 HIGH |
| Materials & Depth | 6 | 6 MEDIUM |
| Ornaments | 6 | 6 MEDIUM |
| **Total** | **44** | |
