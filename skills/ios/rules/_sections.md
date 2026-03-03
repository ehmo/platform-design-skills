# iOS Design Rule Sections

## 1. Layout & Safe Areas (layout)
**Impact:** CRITICAL
**Description:** Correct layout is foundational to every iPhone app. Violating safe areas causes content to be clipped under the status bar, Dynamic Island, or home indicator. Touch targets below 44pt cause mis-taps and frustration. Bottom-of-screen placement for primary actions respects the natural thumb zone. All layouts must adapt across the full range of iPhone screen sizes from iPhone SE to iPhone Pro Max.

## 2. Navigation (nav)
**Impact:** CRITICAL
**Description:** Navigation defines how users move through an app and directly affects whether they can find features and complete tasks. iOS users expect a tab bar at the bottom for top-level sections, large titles in primary views, and swipe-from-left-edge for back. Violating these conventions forces users to relearn interaction patterns they already know, increasing cognitive load and abandonment.

## 3. Typography & Dynamic Type (type)
**Impact:** HIGH
**Description:** Typography is the primary way information is communicated in most apps. Supporting Dynamic Type is both an accessibility requirement and an App Store expectation. Users who set larger text sizes depend on apps respecting that preference. Using built-in text styles ensures automatic scaling, proper weight, and consistent hierarchy across the system.

## 4. Color & Dark Mode (color)
**Impact:** HIGH
**Description:** Color communicates state, hierarchy, and interactivity. Using semantic system colors ensures automatic Dark Mode support and consistency with the platform. Insufficient contrast makes text unreadable for users with low vision. Relying on color alone excludes colorblind users. A single accent color for interactive elements creates a clear, learnable visual language.

## 5. Accessibility (a11y)
**Impact:** CRITICAL
**Description:** Accessibility is not optional on iOS. VoiceOver is used by hundreds of thousands of users daily. Missing accessibility labels make an app completely unusable for blind users. Supporting Bold Text, Reduce Motion, and Increase Contrast respects user preferences set at the system level. Apps that fail accessibility are also likely to fail App Store review for certain categories.

## 6. Gestures & Input (gesture)
**Impact:** HIGH
**Description:** iOS is a gesture-driven platform. Users expect standard gestures like tap, swipe, pinch, and long press to work consistently. Overriding system gestures (edge swipes, notification pull-down) breaks fundamental navigation and creates confusion. All gesture-based interactions must have alternative access paths for users with motor impairments or those using assistive technologies.

## 7. Components (comp)
**Impact:** HIGH
**Description:** UIKit and SwiftUI provide a rich component library that users already understand. Using standard components correctly reduces learning curve and ensures accessibility, Dynamic Type, and Dark Mode support for free. Misusing components (e.g., alerts for non-critical information, hiding tab bars) violates user expectations and creates friction.

## 8. Patterns (pattern)
**Impact:** MEDIUM
**Description:** Common UX patterns like onboarding, loading, and modality shape the overall feel of an app. Skeleton views instead of blocking spinners make apps feel faster. Launch screens that match the first screen eliminate visual jarring. Limiting onboarding to three skippable pages respects user time. These patterns collectively determine whether an app feels native or foreign.

## 9. Privacy & Permissions (privacy)
**Impact:** HIGH
**Description:** Privacy is a core iOS platform value. Requesting permissions without context causes users to deny access reflexively. Explaining why a permission is needed before the system prompt significantly increases grant rates. Supporting Sign in with Apple and not requiring unnecessary account creation respects user privacy. App Tracking Transparency must be respected — denial is the user's right.

## 10. System Integration (system)
**Impact:** MEDIUM
**Description:** Deep system integration makes an app feel like a natural extension of the iPhone. Widgets provide glanceable information on the home screen. App Shortcuts enable Siri and Spotlight access to key actions. Live Activities surface real-time progress on the Lock Screen. Share Sheet integration lets users move data between apps seamlessly. Handling interruptions gracefully ensures the app works within the broader system context.
