# watchOS Design Guidelines — Reference

Quick reference tables and checklists extracted from the main skill.

---

## Screen Dimensions Reference

| Device | Screen Width | Screen Height | Corner Radius |
|--------|-------------|---------------|---------------|
| 41mm (Series 9/10) | 176px | 215px | 36px |
| 45mm (Series 9/10) | 198px | 242px | 39px |
| 49mm (Ultra 2) | 205px | 251px | 40px |

---

## Haptic Feedback Types

| Haptic | Use Case |
|--------|----------|
| `.notification` | General alerts |
| `.directionUp` | Positive event (goal reached, stock up) |
| `.directionDown` | Negative event (stock down, weather warning) |
| `.success` | Action completed successfully |
| `.failure` | Action failed |
| `.retry` | Try again prompt |
| `.start` | Activity beginning |
| `.stop` | Activity ending |
| `.click` | Discrete selection (Crown detent, picker) |

---

## Complication Family Reference

| Family | Shape | Typical Content |
|--------|-------|-----------------|
| `circularSmall` | Small circle | Single value, icon, or gauge |
| `graphicCorner` | Curved, top corners | Gauge with label, or text with icon |
| `graphicCircular` | Larger circle | Gauge, icon with value, or stack |
| `graphicRectangular` | Wide rectangle | Multi-line text, chart, or detailed view |
| `graphicExtraLarge` | Full-width circle | Large gauge or prominent single value |

---

## Evaluation Checklist

Use this checklist when reviewing a watchOS design or implementation.

### Glanceability
- [ ] Can the user understand the primary content within 2 seconds?
- [ ] Is the most important information visible without scrolling?
- [ ] Is body text at least 16pt with sufficient contrast?
- [ ] Are interactions completable in under 5 seconds?

### Digital Crown
- [ ] Does the Crown scroll vertical content?
- [ ] Do value pickers provide haptic detents?
- [ ] Are there no conflicts with system Crown behaviors?

### Navigation
- [ ] Is the primary action reachable within 1 tap from launch?
- [ ] Is the navigation hierarchy 3 levels or fewer?
- [ ] Does every pushed view have a back button?
- [ ] Are top-level sections organized in a TabView (if applicable)?

### Complications
- [ ] Are multiple complication families supported?
- [ ] Do complications work in both tinted and full-color modes?
- [ ] Is complication data updated via TimelineProvider?
- [ ] Does tapping a complication open relevant context?

### Always On
- [ ] Is sensitive data hidden in the dimmed state?
- [ ] Is visual complexity reduced when inactive?
- [ ] Is the update frequency limited to once per minute or less?
- [ ] Is the transition between active and dimmed seamless (no layout shift)?

### Workouts
- [ ] Are live metrics displayed in large, high-contrast text?
- [ ] Are haptics used for milestones?
- [ ] Is auto-pause supported for applicable workout types?
- [ ] Is the workout summary accessible with a single action?

### Notifications
- [ ] Is the Short Look meaningful (title + icon)?
- [ ] Does the Long Look include inline actions?
- [ ] Are haptic types matched to notification urgency?
- [ ] Is notification frequency appropriate (not excessive)?
