# tvOS Design Reference

## Parallax Layer Reference

| Layer | Purpose | Movement Amount |
|-------|---------|-----------------|
| Background | Static backdrop, blurred imagery | Minimal (1-2pt) |
| Midground | Primary artwork or content image | Moderate (3-5pt) |
| Foreground | Title text, logos, badges | Maximum (5-8pt) |

Use Xcode's LSR (Layered Static Image) format or dynamically compose layers at runtime via `TVMonogramView` or custom focus effects.

## Text Size Reference

| Element | Minimum Size | Recommended Size |
|---------|-------------|-----------------|
| Body text | 29pt | 31-35pt |
| Secondary labels | 25pt | 29pt |
| Titles | 48pt | 52-76pt |
| Large headers | 64pt | 76-96pt |
| Buttons | 29pt | 35-38pt |

## Anti-Patterns for TV

Do not bring mobile patterns directly to tvOS:

| Anti-Pattern | Why It Fails | Correct Approach |
|-------------|-------------|-----------------|
| Bottom tab bar | iOS convention; feels wrong on TV | Use top tab bar |
| Small touch targets | Cannot precisely target with swipe navigation | Minimum 250x150pt cards |
| Dense text screens | Unreadable at 10-foot distance | Headlines + short descriptions only |
| Hamburger menus | No tap-to-reveal interaction on TV | Use tab bar or focus-driven menus |
| Pull-to-refresh | No pull gesture on Siri Remote | Auto-refresh or explicit refresh button |
| Toast notifications | Easy to miss on a large TV screen | Use modal alerts or persistent banners |
| Scroll indicators | Thin scrollbars invisible at distance | Use content peek (partially visible next item) |
| Pinch-to-zoom | Multi-finger gestures impossible on Siri Remote | Provide explicit zoom controls |
| Long forms | Keyboard input is painful on tvOS | Pre-fill, use profiles, or offload to iPhone |
| Thin/light typography | Disappears on TV displays | Medium weight minimum |
