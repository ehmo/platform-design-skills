# watchOS Design Rules -- Section Index

All rules organized by category with stable IDs for cross-referencing.

---

## Section 1: Glanceable Design [CRITICAL]

| ID | Rule | Priority |
|----|------|----------|
| W-GL-01 | Primary info visible without scrolling | CRITICAL |
| W-GL-02 | Target 5-second interaction sessions | CRITICAL |
| W-GL-03 | Large, high-contrast text (min 16pt body, 18pt+ titles) | CRITICAL |
| W-GL-04 | Minimize text; use SF Symbols over labels | CRITICAL |
| W-GL-05 | Respect wrist-down (inactive) state | CRITICAL |
| W-GL-06 | One primary data point per screen; clear visual hierarchy | CRITICAL |

**Rationale**: The wrist is the most constrained display surface. Users raise their wrist for a glance, not a reading session. Every pixel must justify its presence.

---

## Section 2: Digital Crown [HIGH]

| ID | Rule | Priority |
|----|------|----------|
| W-DC-01 | Crown is primary scroll input for vertical content | HIGH |
| W-DC-02 | Bind Crown to value pickers with haptic detents | HIGH |
| W-DC-03 | Never override system Crown behaviors | HIGH |
| W-DC-04 | Visual feedback must be frame-synced to Crown rotation | HIGH |

**Rationale**: The Digital Crown is a precision input unique to Apple Watch. It enables interaction without obscuring the small display with fingers.

---

## Section 3: Navigation [HIGH]

| ID | Rule | Priority |
|----|------|----------|
| W-NV-01 | Default to vertical page scrolling | HIGH |
| W-NV-02 | TabView for top-level sections (max 5 tabs) | HIGH |
| W-NV-03 | NavigationStack for hierarchy (max 2-3 levels deep) | HIGH |
| W-NV-04 | Reserve modals for focused single-purpose tasks | HIGH |
| W-NV-05 | Primary action reachable within 1 tap from launch | HIGH |

**Rationale**: Users do not explore on a Watch. They arrive with intent and must reach their goal immediately. Deep hierarchies create frustration on a small screen.

---

## Section 4: Complications [HIGH]

| ID | Rule | Priority |
|----|------|----------|
| W-CP-01 | Support multiple complication families | HIGH |
| W-CP-02 | Provide tinted and full-color variants | HIGH |
| W-CP-03 | Update via TimelineProvider; keep data fresh | HIGH |
| W-CP-04 | Content meaningful without context (include units/labels) | HIGH |
| W-CP-05 | Tap launches app to relevant context | HIGH |

**Rationale**: Complications are the primary engagement surface. A well-designed complication delivers value without the user ever launching the app.

---

## Section 5: Always On Display [MEDIUM]

| ID | Rule | Priority |
|----|------|----------|
| W-AO-01 | Reduce visual complexity in dimmed state | MEDIUM |
| W-AO-02 | Hide sensitive/private data when inactive | MEDIUM |
| W-AO-03 | Limit updates to once per minute in Always On | MEDIUM |
| W-AO-04 | Use system dimming behaviors (no custom dimming) | MEDIUM |
| W-AO-05 | Seamless transition between active and dimmed (no layout shift) | MEDIUM |

**Rationale**: Always On extends the watch face into apps. Poor Always On implementation drains battery and may expose private information.

---

## Section 6: Workouts & Health [MEDIUM]

| ID | Rule | Priority |
|----|------|----------|
| W-WK-01 | Large, high-contrast live metrics during workouts | MEDIUM |
| W-WK-02 | Haptic feedback for milestones and zone changes | MEDIUM |
| W-WK-03 | Auto-pause for running/walking workouts | MEDIUM |
| W-WK-04 | WaterLock during swimming workouts | MEDIUM |
| W-WK-05 | Clear summary screen; save/discard in one action | MEDIUM |

**Rationale**: Workout apps run during physical exertion. The user cannot stop to carefully read or perform complex interactions. Design for motion and sweat.

---

## Section 7: Notifications [MEDIUM]

| ID | Rule | Priority |
|----|------|----------|
| W-NT-01 | Short Look: title + app icon only | MEDIUM |
| W-NT-02 | Long Look: full content + up to 4 action buttons | MEDIUM |
| W-NT-03 | Match haptic type to notification urgency | MEDIUM |
| W-NT-04 | Do not over-notify; batch non-urgent updates | MEDIUM |

**Rationale**: Every Watch notification buzzes the user's wrist. Excessive or poorly designed notifications train users to ignore or disable them entirely.

---

## Cross-Reference: Rule Count Summary

| Section | Count | Priority |
|---------|-------|----------|
| Glanceable Design | 6 | CRITICAL |
| Digital Crown | 4 | HIGH |
| Navigation | 5 | HIGH |
| Complications | 5 | HIGH |
| Always On Display | 5 | MEDIUM |
| Workouts & Health | 5 | MEDIUM |
| Notifications | 4 | MEDIUM |
| **Total** | **34** | |
