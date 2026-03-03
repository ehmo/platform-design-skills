---
name: android-design-guidelines
description: Material Design 3 and Android platform guidelines. Use when building Android apps with Jetpack Compose or XML layouts, implementing Material You, navigation, or accessibility. Triggers on tasks involving Android UI, Compose components, dynamic color, or Material Design compliance.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# Android Platform Design Guidelines — Material Design 3

## 1. Material You & Theming [CRITICAL]

### 1.1 Dynamic Color

Enable dynamic color derived from the user's wallpaper. Dynamic color is the default on Android 12+ and should be the primary theming strategy.

```kotlin
// Compose: Dynamic color theme
@Composable
fun AppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context)
            else dynamicLightColorScheme(context)
        }
        darkTheme -> darkColorScheme()
        else -> lightColorScheme()
    }
    MaterialTheme(
        colorScheme = colorScheme,
        typography = AppTypography,
        content = content
    )
}
```

```xml
<!-- XML: Dynamic color in themes.xml -->
<style name="Theme.App" parent="Theme.Material3.DayNight.NoActionBar">
    <item name="dynamicColorThemeOverlay">@style/ThemeOverlay.Material3.DynamicColors.DayNight</item>
</style>
```

**Rules:**
- R1.1: Always provide a fallback static color scheme for devices below Android 12.
- R1.2: Never hardcode color hex values in components. Always reference color roles from the theme.
- R1.3: Test with at least 3 different wallpapers to verify dynamic color harmony.

### 1.2 Color Roles

Material 3 defines a structured set of color roles. Use them semantically, not aesthetically.

| Role | Usage | On-Role |
|------|-------|---------|
| `primary` | Key actions, active states, FAB | `onPrimary` |
| `primaryContainer` | Less prominent primary elements | `onPrimaryContainer` |
| `secondary` | Supporting UI, filter chips | `onSecondary` |
| `secondaryContainer` | Navigation bar active indicator | `onSecondaryContainer` |
| `tertiary` | Accent, contrast, complementary | `onTertiary` |
| `tertiaryContainer` | Input fields, less prominent accents | `onTertiaryContainer` |
| `surface` | Backgrounds, cards, sheets | `onSurface` |
| `surfaceVariant` | Decorative elements, dividers | `onSurfaceVariant` |
| `error` | Error states, destructive actions | `onError` |
| `errorContainer` | Error backgrounds | `onErrorContainer` |
| `outline` | Borders, dividers | — |
| `outlineVariant` | Subtle borders | — |
| `inverseSurface` | Snackbar background | `inverseOnSurface` |

```kotlin
// Correct: semantic color roles
Text(
    text = "Error message",
    color = MaterialTheme.colorScheme.error
)
Surface(color = MaterialTheme.colorScheme.errorContainer) {
    Text(text = "Error detail", color = MaterialTheme.colorScheme.onErrorContainer)
}

// WRONG: hardcoded colors
Text(text = "Error", color = Color(0xFFB00020)) // Anti-pattern
```

**Rules:**
- R1.4: Every foreground element must use the matching `on` color role for its background (e.g., `onPrimary` text on `primary` background).
- R1.5: Use `surface` and its variants for backgrounds. Never use `primary` or `secondary` as large background areas.
- R1.6: Use `tertiary` sparingly for accent and complementary contrast only.

### 1.3 Light and Dark Themes

Support both light and dark themes. Respect the system setting by default.

```kotlin
// Compose: Detect system theme
val darkTheme = isSystemInDarkTheme()
```

**Rules:**
- R1.7: Always support both light and dark themes. Never ship light-only.
- R1.8: Dark theme surfaces use elevation-based tonal mapping, not pure black (#000000). Use `surface` color roles which handle this automatically.
- R1.9: Provide a manual theme override in app settings (System / Light / Dark).

### 1.4 Custom Color Seeds

When branding requires custom colors, provide a seed color and generate tonal palettes using Material Theme Builder.

```kotlin
// Custom color scheme with brand seed
private val BrandLightColorScheme = lightColorScheme(
    primary = Color(0xFF1B6D2F),
    onPrimary = Color(0xFFFFFFFF),
    primaryContainer = Color(0xFFA4F6A8),
    onPrimaryContainer = Color(0xFF002107),
    // ... generate full palette from seed
)
```

**Rules:**
- R1.10: Generate tonal palettes from seed colors using Material Theme Builder. Never manually pick individual tones.
- R1.11: When using custom colors, still support dynamic color as the default and use custom colors as fallback.

---

## 2. Navigation [CRITICAL]

### 2.1 Navigation Bar (Bottom)

The primary navigation pattern for phones with 3-5 top-level destinations.

```kotlin
// Compose: Navigation Bar
NavigationBar {
    items.forEachIndexed { index, item ->
        NavigationBarItem(
            icon = {
                Icon(
                    imageVector = if (selectedItem == index) item.filledIcon else item.outlinedIcon,
                    contentDescription = item.label
                )
            },
            label = { Text(item.label) },
            selected = selectedItem == index,
            onClick = { selectedItem = index }
        )
    }
}
```

**Rules:**
- R2.1: Use Navigation Bar for 3-5 top-level destinations on compact screens. Never use for fewer than 3 or more than 5.
- R2.2: Always show labels on navigation bar items. Icon-only navigation bars are not permitted.
- R2.3: Use filled icons for the selected state and outlined icons for unselected states.
- R2.4: The active indicator uses `secondaryContainer` color. Do not override this.

### 2.2 Navigation Rail

For medium and expanded screens (tablets, foldables, desktop).

```kotlin
// Compose: Navigation Rail for larger screens
NavigationRail(
    header = {
        FloatingActionButton(
            onClick = { /* primary action */ },
            containerColor = MaterialTheme.colorScheme.tertiaryContainer
        ) {
            Icon(Icons.Default.Add, contentDescription = "Create")
        }
    }
) {
    items.forEachIndexed { index, item ->
        NavigationRailItem(
            icon = { Icon(item.icon, contentDescription = item.label) },
            label = { Text(item.label) },
            selected = selectedItem == index,
            onClick = { selectedItem = index }
        )
    }
}
```

**Rules:**
- R2.5: Use Navigation Rail on medium (600-839dp) and expanded (840dp+) window sizes. Pair it with Navigation Bar on compact.
- R2.6: Optionally include a FAB in the rail header for the primary action.
- R2.7: Labels are optional on the rail but recommended for clarity.

### 2.3 Navigation Drawer

For 5+ destinations or complex navigation hierarchies, typically on expanded screens.

```kotlin
// Compose: Permanent Navigation Drawer for large screens
PermanentNavigationDrawer(
    drawerContent = {
        PermanentDrawerSheet {
            Text("App Name", modifier = Modifier.padding(16.dp),
                 style = MaterialTheme.typography.titleMedium)
            HorizontalDivider()
            items.forEach { item ->
                NavigationDrawerItem(
                    label = { Text(item.label) },
                    selected = item == selectedItem,
                    onClick = { selectedItem = item },
                    icon = { Icon(item.icon, contentDescription = null) }
                )
            }
        }
    }
) {
    Scaffold { /* page content */ }
}
```

**Rules:**
- R2.8: Use modal drawer on compact screens, permanent drawer on expanded screens.
- R2.9: Group drawer items into sections with dividers and section headers.

### 2.4 Predictive Back Gesture

Android 13+ supports predictive back with an animation preview.

```kotlin
// Compose: Predictive back handling
val predictiveBackHandler = remember { PredictiveBackHandler(enabled = true) { progress ->
    // Animate based on progress (0.0 to 1.0)
}}
```

```xml
<!-- AndroidManifest.xml: opt in to predictive back -->
<application android:enableOnBackInvokedCallback="true">
```

**Rules:**
- R2.10: Opt in to predictive back in the manifest. Handle `OnBackInvokedCallback` instead of overriding `onBackPressed()`.
- R2.11: The system back gesture navigates back in the navigation stack. The Up button (toolbar arrow) navigates up in the app hierarchy. These may differ.
- R2.12: Never intercept system back to show "are you sure?" dialogs unless there is unsaved user input.

### 2.5 Navigation Component Selection

| Screen Size | 3-5 Destinations | 5+ Destinations |
|-------------|-------------------|-----------------|
| Compact (< 600dp) | Navigation Bar | Modal Drawer + Navigation Bar |
| Medium (600-839dp) | Navigation Rail | Modal Drawer + Navigation Rail |
| Expanded (840dp+) | Navigation Rail | Permanent Drawer |

---

## 3. Layout & Responsive [HIGH]

### 3.1 Window Size Classes

Use window size classes for adaptive layouts, not raw pixel breakpoints.

```kotlin
// Compose: Window size classes
val windowSizeClass = calculateWindowSizeClass(this)
when (windowSizeClass.widthSizeClass) {
    WindowWidthSizeClass.Compact -> CompactLayout()
    WindowWidthSizeClass.Medium -> MediumLayout()
    WindowWidthSizeClass.Expanded -> ExpandedLayout()
}
```

| Class | Width | Typical Device | Columns |
|-------|-------|----------------|---------|
| Compact | < 600dp | Phone portrait | 4 |
| Medium | 600-839dp | Tablet portrait, foldable | 8 |
| Expanded | 840dp+ | Tablet landscape, desktop | 12 |

**Rules:**
- R3.1: Always use `WindowSizeClass` from `material3-window-size-class` for responsive layout decisions.
- R3.2: Never use fixed pixel breakpoints. Device categories are fluid.
- R3.3: Support all three width size classes. At minimum, compact and expanded.

### 3.2 Material Grid

Apply canonical Material grid margins and gutters.

| Size Class | Margins | Gutters | Columns |
|------------|---------|---------|---------|
| Compact | 16dp | 8dp | 4 |
| Medium | 24dp | 16dp | 8 |
| Expanded | 24dp | 24dp | 12 |

**Rules:**
- R3.4: Content should not span the full width on expanded screens. Use a max content width of ~840dp or list-detail layout.
- R3.5: Apply consistent horizontal margins matching the grid spec.

### 3.3 Edge-to-Edge Display

Android 15+ enforces edge-to-edge. All apps should draw behind system bars.

```kotlin
// Compose: Edge-to-edge setup
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        enableEdgeToEdge()
        super.onCreate(savedInstanceState)
        setContent {
            Scaffold(
                modifier = Modifier.fillMaxSize(),
                // Scaffold handles insets for top/bottom bars automatically
            ) { innerPadding ->
                Content(modifier = Modifier.padding(innerPadding))
            }
        }
    }
}
```

**Rules:**
- R3.6: Call `enableEdgeToEdge()` before `setContent`. Draw behind both status bar and navigation bar.
- R3.7: Use `WindowInsets` to pad content away from system bars. `Scaffold` handles this for top bar and bottom bar content automatically.
- R3.8: Scrollable content should scroll behind transparent system bars with appropriate inset padding at the top and bottom of the list.

### 3.4 Foldable Device Support

```kotlin
// Compose: Detect fold posture
val foldingFeatures = WindowInfoTracker.getOrCreate(context)
    .windowLayoutInfo(context)
    .collectAsState(initial = WindowLayoutInfo(emptyList()))
```

**Rules:**
- R3.9: Detect hinge/fold position and avoid placing critical content across the fold.
- R3.10: Use `ListDetailPaneScaffold` or `SupportingPaneScaffold` from Material3 adaptive library for foldable-aware layouts.

---

## 4. Typography [HIGH]

### 4.1 Material Type Scale

| Role | Default Size | Default Weight | Usage |
|------|-------------|----------------|-------|
| displayLarge | 57sp | 400 | Hero text, onboarding |
| displayMedium | 45sp | 400 | Large feature text |
| displaySmall | 36sp | 400 | Prominent display |
| headlineLarge | 32sp | 400 | Screen titles |
| headlineMedium | 28sp | 400 | Section headers |
| headlineSmall | 24sp | 400 | Card titles |
| titleLarge | 22sp | 400 | Top app bar title |
| titleMedium | 16sp | 500 | Tabs, navigation |
| titleSmall | 14sp | 500 | Subtitles |
| bodyLarge | 16sp | 400 | Primary body text |
| bodyMedium | 14sp | 400 | Secondary body text |
| bodySmall | 12sp | 400 | Captions |
| labelLarge | 14sp | 500 | Buttons, prominent labels |
| labelMedium | 12sp | 500 | Chips, smaller labels |
| labelSmall | 11sp | 500 | Timestamps, annotations |

```kotlin
// Compose: Custom typography
val AppTypography = Typography(
    displayLarge = TextStyle(
        fontFamily = FontFamily(Font(R.font.brand_regular)),
        fontWeight = FontWeight.Normal,
        fontSize = 57.sp,
        lineHeight = 64.sp,
        letterSpacing = (-0.25).sp
    ),
    bodyLarge = TextStyle(
        fontFamily = FontFamily(Font(R.font.brand_regular)),
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    )
    // ... define all 15 roles
)
```

**Rules:**
- R4.1: Always use `sp` units for text sizes to support user font scaling preferences.
- R4.2: Never set text below 12sp for body content. Labels may go to 11sp minimum.
- R4.3: Reference typography roles from `MaterialTheme.typography`, not hardcoded sizes.
- R4.4: Support dynamic type scaling. Test at 200% font scale. Ensure no text is clipped or overlapping.
- R4.5: Line height should be approximately 1.2-1.5x the font size for readability.

---

## 5. Components [HIGH]

### 5.1 Floating Action Button (FAB)

The FAB represents the single most important action on a screen.

```kotlin
// Compose: FAB variants
// Standard FAB
FloatingActionButton(onClick = { /* action */ }) {
    Icon(Icons.Default.Add, contentDescription = "Create new item")
}

// Extended FAB (with label - preferred for clarity)
ExtendedFloatingActionButton(
    onClick = { /* action */ },
    icon = { Icon(Icons.Default.Edit, contentDescription = null) },
    text = { Text("Compose") }
)

// Large FAB
LargeFloatingActionButton(onClick = { /* action */ }) {
    Icon(Icons.Default.Add, contentDescription = "Create", modifier = Modifier.size(36.dp))
}
```

**Rules:**
- R5.1: Use at most one FAB per screen. It represents the primary action.
- R5.2: Place the FAB at the bottom-end of the screen. On screens with a Navigation Bar, the FAB floats above it.
- R5.3: The FAB should use `primaryContainer` color by default. Use `tertiaryContainer` for secondary screens.
- R5.4: Prefer `ExtendedFloatingActionButton` with a label for clarity. Collapse to icon-only on scroll if needed.

### 5.2 Top App Bar

```kotlin
// Compose: Top app bar variants
// Small (default)
TopAppBar(
    title = { Text("Page Title") },
    navigationIcon = {
        IconButton(onClick = { /* navigate up */ }) {
            Icon(Icons.AutoMirrored.Filled.ArrowBack, contentDescription = "Back")
        }
    },
    actions = {
        IconButton(onClick = { /* search */ }) {
            Icon(Icons.Default.Search, contentDescription = "Search")
        }
    }
)

// Medium — expands title area
MediumTopAppBar(
    title = { Text("Section Title") },
    scrollBehavior = TopAppBarDefaults.enterAlwaysScrollBehavior()
)

// Large — for prominent titles
LargeTopAppBar(
    title = { Text("Screen Title") },
    scrollBehavior = TopAppBarDefaults.exitUntilCollapsedScrollBehavior()
)
```

**Rules:**
- R5.5: Use `TopAppBar` (small) for most screens. Use `MediumTopAppBar` or `LargeTopAppBar` for prominent section or screen titles.
- R5.6: Connect scroll behavior to the app bar so it collapses/expands with content scrolling.
- R5.7: Limit action icons to 2-3. Overflow additional actions into a more menu.

### 5.3 Bottom Sheets

```kotlin
// Compose: Modal bottom sheet
ModalBottomSheet(
    onDismissRequest = { showSheet = false },
    sheetState = rememberModalBottomSheetState()
) {
    Column(modifier = Modifier.padding(16.dp)) {
        Text("Sheet Title", style = MaterialTheme.typography.titleLarge)
        Spacer(modifier = Modifier.height(16.dp))
        // Sheet content
    }
}
```

**Rules:**
- R5.8: Use modal bottom sheets for non-critical supplementary content. Use standard bottom sheets for persistent content.
- R5.9: Bottom sheets must have a visible drag handle for discoverability.
- R5.10: Sheet content must be scrollable if it can exceed the visible area.

### 5.4 Dialogs

```kotlin
// Compose: Alert dialog
AlertDialog(
    onDismissRequest = { showDialog = false },
    title = { Text("Discard draft?") },
    text = { Text("Your unsaved changes will be lost.") },
    confirmButton = {
        TextButton(onClick = { /* confirm */ }) { Text("Discard") }
    },
    dismissButton = {
        TextButton(onClick = { showDialog = false }) { Text("Cancel") }
    }
)
```

**Rules:**
- R5.11: Dialogs interrupt the user. Use them only for critical decisions requiring immediate attention.
- R5.12: Confirm button uses a text button, not a filled button. The dismiss button is always on the left.
- R5.13: Dialog titles should be concise questions or statements. Body text provides context.

### 5.5 Snackbar

```kotlin
// Compose: Snackbar with action
val snackbarHostState = remember { SnackbarHostState() }
Scaffold(snackbarHost = { SnackbarHost(snackbarHostState) }) {
    // trigger snackbar
    LaunchedEffect(key) {
        val result = snackbarHostState.showSnackbar(
            message = "Item archived",
            actionLabel = "Undo",
            duration = SnackbarDuration.Short
        )
        if (result == SnackbarResult.ActionPerformed) { /* undo */ }
    }
}
```

**Rules:**
- R5.14: Use snackbars for brief, non-critical feedback. They auto-dismiss and should not contain critical information.
- R5.15: Snackbars appear at the bottom of the screen, above the Navigation Bar and below the FAB.
- R5.16: Include an action (e.g., "Undo") when the operation is reversible. Limit to one action.

### 5.6 Chips

```kotlin
// Filter Chip
FilterChip(
    selected = isSelected,
    onClick = { isSelected = !isSelected },
    label = { Text("Filter") },
    leadingIcon = if (isSelected) {
        { Icon(Icons.Default.Check, contentDescription = null, modifier = Modifier.size(18.dp)) }
    } else null
)

// Assist Chip
AssistChip(
    onClick = { /* action */ },
    label = { Text("Add to calendar") },
    leadingIcon = { Icon(Icons.Default.CalendarToday, contentDescription = null) }
)
```

**Rules:**
- R5.17: Use `FilterChip` for toggling filters, `AssistChip` for smart suggestions, `InputChip` for user-entered content (tags), `SuggestionChip` for dynamically generated suggestions.
- R5.18: Chips should be arranged in a horizontally scrollable row or a flow layout, not stacked vertically.

### 5.7 Component Selection Guide

| Need | Component |
|------|-----------|
| Primary screen action | FAB |
| Brief feedback | Snackbar |
| Critical decision | Dialog |
| Supplementary content | Bottom Sheet |
| Toggle filter | Filter Chip |
| User-entered tag | Input Chip |
| Smart suggestion | Assist Chip |
| Content group | Card |
| Vertical list of items | LazyColumn with ListItem |
| Segmented option (2-5) | SegmentedButton |
| Binary toggle | Switch |
| Selection from list | Radio buttons or exposed dropdown menu |

---

## 6. Accessibility [CRITICAL]

### 6.1 TalkBack and Content Descriptions

```kotlin
// Compose: Accessible components
Icon(
    Icons.Default.Favorite,
    contentDescription = "Add to favorites" // Descriptive, not "heart icon"
)

// Decorative elements
Icon(
    Icons.Default.Star,
    contentDescription = null // null for purely decorative
)

// Merge semantics for compound elements
Row(modifier = Modifier.semantics(mergeDescendants = true) {}) {
    Icon(Icons.Default.Event, contentDescription = null)
    Text("March 15, 2026")
}

// Custom actions
Box(modifier = Modifier.semantics {
    customActions = listOf(
        CustomAccessibilityAction("Archive") { /* archive */ true },
        CustomAccessibilityAction("Delete") { /* delete */ true }
    )
})
```

**Rules:**
- R6.1: Every interactive element must have a `contentDescription` (or `null` if purely decorative).
- R6.2: Content descriptions must describe the action or meaning, not the visual appearance. Say "Add to favorites" not "Heart icon."
- R6.3: Use `mergeDescendants = true` to group related elements into a single TalkBack focus unit (e.g., a list item with icon + text + subtitle).
- R6.4: Provide `customActions` for swipe-to-dismiss or long-press actions so TalkBack users can access them.

### 6.2 Touch Targets

```kotlin
// Compose: Ensure minimum touch target
IconButton(onClick = { /* action */ }) {
    // IconButton already provides 48dp minimum touch target
    Icon(Icons.Default.Close, contentDescription = "Close")
}

// Manual minimum touch target
Box(
    modifier = Modifier
        .sizeIn(minWidth = 48.dp, minHeight = 48.dp)
        .clickable { /* action */ },
    contentAlignment = Alignment.Center
) {
    Icon(Icons.Default.Info, contentDescription = "Info", modifier = Modifier.size(24.dp))
}
```

**Rules:**
- R6.5: All interactive elements must have a minimum touch target of 48x48dp. Material 3 components handle this by default.
- R6.6: Do not reduce touch targets to save space. Use padding to increase the touchable area if the visual element is smaller.

### 6.3 Color Contrast and Visual

**Rules:**
- R6.7: Text contrast ratio must be at least 4.5:1 for normal text and 3:1 for large text (18sp+ or 14sp+ bold) against its background.
- R6.8: Never use color as the only means of conveying information. Pair with icons, text, or patterns.
- R6.9: Support "bold text" and "high contrast" accessibility settings.

### 6.4 Focus and Traversal

```kotlin
// Compose: Custom focus order
Column {
    var focusRequester = remember { FocusRequester() }
    TextField(
        modifier = Modifier.focusRequester(focusRequester),
        value = text,
        onValueChange = { text = it }
    )
    LaunchedEffect(Unit) {
        focusRequester.requestFocus() // Auto-focus on screen load
    }
}
```

**Rules:**
- R6.10: Focus order must follow a logical reading sequence (top-to-bottom, start-to-end). Avoid custom `focusOrder` unless the default is incorrect.
- R6.11: After navigation or dialog dismissal, move focus to the most logical target element.
- R6.12: All screens must be fully operable using TalkBack, Switch Access, and external keyboard.

---

## 7. Gestures & Input [MEDIUM]

### 7.1 System Gestures

**Rules:**
- R7.1: Never place interactive elements within the system gesture inset zones (bottom 20dp, left/right 24dp edges) as they conflict with system navigation gestures.
- R7.2: Use `WindowInsets.systemGestures` to detect and avoid gesture conflict zones.

### 7.2 Common Gesture Patterns

```kotlin
// Compose: Pull to refresh
PullToRefreshBox(
    isRefreshing = isRefreshing,
    onRefresh = { viewModel.refresh() }
) {
    LazyColumn { /* content */ }
}

// Compose: Swipe to dismiss
SwipeToDismissBox(
    state = rememberSwipeToDismissBoxState(),
    backgroundContent = {
        Box(
            modifier = Modifier.fillMaxSize().background(MaterialTheme.colorScheme.error),
            contentAlignment = Alignment.CenterEnd
        ) {
            Icon(Icons.Default.Delete, contentDescription = "Delete",
                 tint = MaterialTheme.colorScheme.onError)
        }
    }
) {
    ListItem(headlineContent = { Text("Swipeable item") })
}
```

**Rules:**
- R7.3: All swipe-to-dismiss actions must be undoable (show snackbar with undo) or require confirmation.
- R7.4: Provide alternative non-gesture ways to trigger all gesture-based actions (for accessibility).
- R7.5: Apply Material ripple effect on all tappable elements. Compose `clickable` modifier includes ripple by default.

### 7.3 Long Press

**Rules:**
- R7.6: Use long press for contextual menus and multi-select mode. Never use it as the only way to access a feature.
- R7.7: Provide haptic feedback on long press via `HapticFeedbackType.LongPress`.

---

## 8. Notifications [MEDIUM]

### 8.1 Notification Channels

```kotlin
// Create notification channel (required for Android 8+)
val channel = NotificationChannel(
    "messages",
    "Messages",
    NotificationManager.IMPORTANCE_HIGH
).apply {
    description = "New message notifications"
    enableLights(true)
    lightColor = Color.BLUE
}
notificationManager.createNotificationChannel(channel)
```

| Importance | Behavior | Use For |
|-----------|----------|---------|
| IMPORTANCE_HIGH | Sound + heads-up | Messages, calls |
| IMPORTANCE_DEFAULT | Sound | Social updates, emails |
| IMPORTANCE_LOW | No sound | Recommendations |
| IMPORTANCE_MIN | Silent, no status bar | Weather, ongoing |

**Rules:**
- R8.1: Create separate notification channels for each distinct notification type. Users can configure each independently.
- R8.2: Choose importance levels conservatively. Overusing `IMPORTANCE_HIGH` leads users to disable notifications entirely.
- R8.3: All notifications must have a tap action (PendingIntent) that navigates to relevant content.
- R8.4: Include a `contentDescription` in notification icons for accessibility.

### 8.2 Notification Design

**Rules:**
- R8.5: Use `MessagingStyle` for conversations. Include sender name and avatar.
- R8.6: Add direct reply actions to messaging notifications.
- R8.7: Provide a "Mark as read" action on message notifications.
- R8.8: Use expandable notifications (`BigTextStyle`, `BigPictureStyle`, `InboxStyle`) for rich content.
- R8.9: Foreground service notifications must accurately describe the ongoing operation and provide a stop action where appropriate.

---

## 9. Permissions & Privacy [HIGH]

### 9.1 Runtime Permissions

```kotlin
// Compose: Permission request
val permissionState = rememberPermissionState(Manifest.permission.CAMERA)

if (permissionState.status.isGranted) {
    CameraPreview()
} else {
    Column {
        Text("Camera access is needed to scan QR codes.")
        Button(onClick = { permissionState.launchPermissionRequest() }) {
            Text("Grant Camera Access")
        }
    }
}
```

**Rules:**
- R9.1: Request permissions in context, at the moment they are needed, not at app launch.
- R9.2: Always explain why the permission is needed before requesting it (rationale screen).
- R9.3: Gracefully handle permission denial. Provide degraded functionality rather than blocking the user.
- R9.4: Never request permissions you do not actively use. Google Play will reject apps with unnecessary permissions.

### 9.2 Privacy-Preserving APIs

```kotlin
// Photo picker: no permission needed
val pickMedia = rememberLauncherForActivityResult(
    ActivityResultContracts.PickVisualMedia()
) { uri ->
    uri?.let { /* handle selected media */ }
}
pickMedia.launch(PickVisualMediaRequest(ActivityResultContracts.PickVisualMedia.ImageOnly))
```

**Rules:**
- R9.5: Use the Photo Picker (Android 13+) instead of requesting `READ_MEDIA_IMAGES`. No permission needed.
- R9.6: Use `ACCESS_COARSE_LOCATION` (approximate) unless precise location is essential for functionality.
- R9.7: Prefer one-time permissions for camera and microphone in non-recording contexts.
- R9.8: Display a privacy indicator when camera or microphone is actively in use.

---

## 10. System Integration [MEDIUM]

### 10.1 Widgets

```kotlin
// Compose Glance API widget
class TaskWidget : GlanceAppWidget() {
    override suspend fun provideGlance(context: Context, id: GlanceId) {
        provideContent {
            GlanceTheme {
                Column(
                    modifier = GlanceModifier
                        .fillMaxSize()
                        .background(GlanceTheme.colors.widgetBackground)
                        .padding(16.dp)
                ) {
                    Text(
                        text = "Tasks",
                        style = TextStyle(fontWeight = FontWeight.Bold,
                                         color = GlanceTheme.colors.onSurface)
                    )
                    // Widget content
                }
            }
        }
    }
}
```

**Rules:**
- R10.1: Use Glance API for new widgets. Support dynamic color via `GlanceTheme`.
- R10.2: Widgets must have a default configuration and work immediately after placement.
- R10.3: Provide multiple widget sizes (small, medium, large) where practical.
- R10.4: Use rounded corners matching the system widget shape (`system_app_widget_background_radius`).

### 10.2 App Shortcuts

```xml
<!-- shortcuts.xml -->
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:shortcutId="compose"
        android:enabled="true"
        android:shortcutShortLabel="@string/compose_short"
        android:shortcutLongLabel="@string/compose_long"
        android:icon="@drawable/ic_shortcut_compose">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetPackage="com.example.app"
            android:targetClass="com.example.app.ComposeActivity" />
    </shortcut>
</shortcuts>
```

**Rules:**
- R10.5: Provide 2-4 static shortcuts for common actions. Support dynamic shortcuts for recent content.
- R10.6: Shortcut icons should be simple, recognizable silhouettes on a circular background.
- R10.7: Test shortcuts with long-press on the app icon and in the Settings > Apps shortcut list.

### 10.3 Deep Links and Share

**Rules:**
- R10.8: Support Android App Links (verified deep links) for all public content URLs.
- R10.9: Implement the share sheet with `ShareCompat` or `Intent.createChooser`. Provide rich previews with title, description, and thumbnail.
- R10.10: Handle incoming share intents with appropriate content type filtering.

---

## Design Evaluation Checklist

Use this checklist to evaluate Android UI implementations:

### Theme & Color
- [ ] Dynamic color enabled with static fallback
- [ ] All colors reference Material theme roles (no hardcoded hex)
- [ ] Light and dark themes both supported
- [ ] On-colors match their background color roles
- [ ] Custom colors generated from seed via Material Theme Builder

### Navigation
- [ ] Correct navigation component for screen size and destination count
- [ ] Navigation bar labels always visible
- [ ] Predictive back gesture opted in and handled
- [ ] Up vs Back behavior correct

### Layout
- [ ] All three window size classes supported
- [ ] Edge-to-edge with proper inset handling
- [ ] Content does not span full width on large screens
- [ ] Foldable hinge area respected

### Typography
- [ ] All text uses sp units
- [ ] All text references MaterialTheme.typography roles
- [ ] Tested at 200% font scale with no clipping
- [ ] Minimum 12sp body, 11sp labels

### Components
- [ ] At most one FAB per screen
- [ ] Top app bar connected to scroll behavior
- [ ] Snackbars used for non-critical feedback only
- [ ] Dialogs reserved for critical interruptions

### Accessibility
- [ ] All interactive elements have contentDescription
- [ ] All touch targets >= 48dp
- [ ] Color contrast >= 4.5:1 for text
- [ ] No information conveyed by color alone
- [ ] Full TalkBack traversal tested
- [ ] Switch Access and keyboard navigation work

### Gestures
- [ ] No interactive elements in system gesture zones
- [ ] All gesture actions have non-gesture alternatives
- [ ] Swipe-to-dismiss is undoable

### Notifications
- [ ] Separate channels for each notification type
- [ ] Appropriate importance levels
- [ ] Tap action navigates to relevant content

### Permissions
- [ ] Permissions requested in context, not at launch
- [ ] Rationale shown before permission request
- [ ] Graceful degradation on denial
- [ ] Photo Picker used instead of media permission

### System Integration
- [ ] Widgets use Glance API with dynamic color
- [ ] App shortcuts provided for common actions
- [ ] Deep links handled for public content

---

## Anti-Patterns

| Anti-Pattern | Why It Is Wrong | Correct Approach |
|-------------|----------------|------------------|
| Hardcoded color hex values | Breaks dynamic color and dark theme | Use `MaterialTheme.colorScheme` roles |
| Using `dp` for text size | Ignores user font scaling | Use `sp` units |
| Custom bottom navigation bar | Inconsistent with platform | Use Material `NavigationBar` |
| Navigation bar without labels | Violates Material guidelines | Always show labels |
| Dialog for non-critical info | Interrupts user unnecessarily | Use Snackbar or Bottom Sheet |
| FAB for secondary actions | Dilutes primary action prominence | One FAB for the primary action only |
| `onBackPressed()` override | Deprecated; breaks predictive back | Use `OnBackInvokedCallback` |
| Touch targets < 48dp | Accessibility violation | Ensure minimum 48x48dp |
| Permission request at launch | Users deny without context | Request in context with rationale |
| Pure black (#000000) dark theme | Eye strain; not Material 3 | Use Material surface color roles |
| Icon-only navigation bar | Users cannot identify destinations | Always include text labels |
| Full-width content on tablets | Wastes space; poor readability | Max width or list-detail layout |
| `READ_EXTERNAL_STORAGE` for photos | Unnecessary since Android 13 | Use Photo Picker API |
| Blocking UI on permission denial | Punishes the user | Graceful degradation |
| Manual color palette selection | Inconsistent tonal relationships | Use Material Theme Builder |
