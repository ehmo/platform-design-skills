# tvOS Design Rules -- Sectioned Reference

Quick-access reference organized by category. Each rule has a severity and unique ID for citation.

---

## Section 1: Focus-Based Navigation

| ID | Rule | Severity |
|----|------|----------|
| FOCUS-01 | Every interactive element must have a clearly visible focus state | CRITICAL |
| FOCUS-02 | Focus movement must be predictable and follow logical spatial layout | CRITICAL |
| FOCUS-03 | Use UIFocusGuide to bridge gaps between focusable elements | CRITICAL |
| FOCUS-04 | Apply parallax effect to focused items using layered images (LSR) | CRITICAL |
| FOCUS-05 | Minimum card size 250x150pt for comfortable focus targeting | CRITICAL |
| FOCUS-06 | Provide a default focused element on every screen | CRITICAL |
| FOCUS-07 | Preserve focus memory when returning to a screen | HIGH |
| FOCUS-08 | Never trap focus; users must always be able to move away | CRITICAL |

### Key APIs
- `UIFocusEnvironment`, `UIFocusGuide`, `UIFocusSystem`
- `didUpdateFocus(in:with:)` for tracking focus changes
- `preferredFocusEnvironments` for setting default focus
- Layered images: LSR format via Xcode asset catalog

---

## Section 2: Siri Remote

| ID | Rule | Severity |
|----|------|----------|
| REMOTE-01 | Touch surface swipes control focus; clicks select | CRITICAL |
| REMOTE-02 | Menu button must always navigate back | CRITICAL |
| REMOTE-03 | Play/Pause button must control media playback | CRITICAL |
| REMOTE-04 | Never require complex or multi-finger gestures | CRITICAL |
| REMOTE-05 | Swipe directions must be intuitive and axis-consistent | HIGH |
| REMOTE-06 | Support Siri voice input for search and text entry | HIGH |
| REMOTE-07 | Provide immediate click feedback (visual response) | HIGH |

### Input Model
- **Touch surface**: Swipe (directional), click (select), long press (context)
- **Menu button**: Back/dismiss -- do not override
- **Play/Pause**: Media transport -- always respect
- **Siri button**: Voice input and search
- **Volume**: System-controlled, do not intercept
- Game controllers supported as secondary input via `GCController`

---

## Section 3: 10-Foot UI

| ID | Rule | Severity |
|----|------|----------|
| DISTANCE-01 | Minimum body text 29pt; titles 48pt+ | HIGH |
| DISTANCE-02 | High contrast: light text on dark backgrounds | HIGH |
| DISTANCE-03 | Limit text per screen; headlines and short descriptions only | HIGH |
| DISTANCE-04 | High-resolution imagery: 1920x1080 minimum, 3840x2160 for 4K | HIGH |
| DISTANCE-05 | Simple, spacious layouts; avoid dense grids | HIGH |
| DISTANCE-06 | Keep content within TV-safe area (60pt inset from edges) | HIGH |
| DISTANCE-07 | Avoid thin fonts and hairline borders; use medium weight minimum | MEDIUM |

### Screen Specifications
- **Resolution**: 1920x1080 (HD), 3840x2160 (4K)
- **Safe area inset**: 60pt from all edges
- **Color space**: Display P3 (wide color) supported
- **Frame rate**: 24fps, 30fps, 60fps content supported
- **HDR**: Dolby Vision and HDR10 supported on Apple TV 4K

---

## Section 4: Top Shelf

| ID | Rule | Severity |
|----|------|----------|
| SHELF-01 | Provide a TVTopShelfProvider extension with dynamic content | HIGH |
| SHELF-02 | Use correct layout: inset banner or sectioned content | HIGH |
| SHELF-03 | Every Top Shelf item must deep-link into corresponding content | HIGH |
| SHELF-04 | Use high-quality imagery at recommended dimensions | HIGH |
| SHELF-05 | Keep content fresh; update regularly | MEDIUM |

### Image Dimensions

| Layout | Asset | Size (@1x) | Size (@2x) |
|--------|-------|-----------|-----------|
| Inset banner | Wide image | 1940x624pt | 3880x1248pt |
| Sectioned | Poster | 404x608pt | 808x1216pt |

### Implementation
- Conform to `TVTopShelfProvider` protocol
- Return `TVTopShelfSectionedContent` or `TVTopShelfInsetContent`
- Each `TVTopShelfItem` takes a `URL` for deep linking
- System caches and refreshes on its own schedule; call `TVTopShelfContentProvider.topShelfContentDidChange()` to request update

---

## Section 5: Media & Playback

| ID | Rule | Severity |
|----|------|----------|
| MEDIA-01 | Use standard transport controls (play, pause, skip, scrub) | MEDIUM |
| MEDIA-02 | Swipe-down during playback shows info overlay | MEDIUM |
| MEDIA-03 | Support scrubbing via touch surface with thumbnail previews | MEDIUM |
| MEDIA-04 | Provide subtitle and audio track selection | MEDIUM |
| MEDIA-05 | Support Picture in Picture for video content | LOW |
| MEDIA-06 | Remember and resume playback position | MEDIUM |
| MEDIA-07 | Handle interruptions gracefully; save position on app switch | MEDIUM |

### Key APIs
- `AVPlayerViewController` -- standard playback UI with transport controls
- `AVPlayerItem` -- media item with metadata
- `AVPictureInPictureController` -- PiP management
- `AVMediaSelectionGroup` -- subtitle and audio track selection
- `NowPlayingInfo` via `MPNowPlayingInfoCenter` for system integration

---

## Section 6: Tab Bar

| ID | Rule | Severity |
|----|------|----------|
| TAB-01 | Tab bar at top of screen (not bottom) | MEDIUM |
| TAB-02 | Translucent tab bar overlays content with blur | MEDIUM |
| TAB-03 | Use 3-7 tabs | MEDIUM |
| TAB-04 | Every tab must have a text label | MEDIUM |
| TAB-05 | Focus on tab bar should feel lightweight and smooth | LOW |
| TAB-06 | Remember selected tab across app launches | LOW |

### Implementation
- Use `UITabBarController` which automatically positions at top on tvOS
- Tab bar items: `UITabBarItem(title:image:tag:)`
- Store selected tab index in `UserDefaults` for persistence
- Content beneath tab bar should use `additionalSafeAreaInsets` or standard layout guides

---

## Rule Count by Severity

| Severity | Count |
|----------|-------|
| CRITICAL | 10 |
| HIGH | 12 |
| MEDIUM | 10 |
| LOW | 2 |
| **Total** | **34** |
