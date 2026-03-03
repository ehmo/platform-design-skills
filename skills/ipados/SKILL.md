---
name: ipados-design-guidelines
description: Apple Human Interface Guidelines for iPad. Use when building iPad-optimized interfaces, implementing multitasking, pointer support, keyboard shortcuts, or responsive layouts. Triggers on tasks involving iPad, Split View, Stage Manager, sidebar navigation, or trackpad support.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# iPadOS Design Guidelines

Comprehensive rules for building iPad-native apps following Apple's Human Interface Guidelines. iPad is not a big iPhone -- it demands adaptive layouts, multitasking support, pointer interactions, keyboard shortcuts, and inter-app drag and drop. These rules extend iOS patterns for the larger, more capable canvas.

---

## 1. Responsive Layout (CRITICAL)

### 1.1 Use Adaptive Size Classes

iPad presents two horizontal size classes: **regular** (full screen, large splits) and **compact** (Slide Over, narrow splits). Design for both. Never hardcode dimensions.

```swift
struct AdaptiveView: View {
    @Environment(\.horizontalSizeClass) var sizeClass

    var body: some View {
        if sizeClass == .regular {
            TwoColumnLayout()
        } else {
            StackedLayout()
        }
    }
}
```

### 1.2 Don't Scale Up iPhone UI

iPad layouts must be purpose-built. Stretching an iPhone layout across a 13" display wastes space and feels wrong. Use multi-column layouts, master-detail patterns, and increased information density in regular width.

### 1.3 Support All iPad Screen Sizes

Design for the full range: iPad Mini (8.3"), iPad (10.9"), iPad Air (11"/13"), and iPad Pro (11"/13"). Use flexible layouts that redistribute content rather than simply scaling.

### 1.4 Column-Based Layouts for Regular Width

In regular width, organize content into columns. Two-column is the most common (sidebar + detail). Three-column works for deep hierarchies (sidebar + list + detail). Avoid single-column full-width layouts on large screens.

```swift
struct ThreeColumnLayout: View {
    var body: some View {
        NavigationSplitView {
            SidebarView()
        } content: {
            ContentListView()
        } detail: {
            DetailView()
        }
    }
}
```

### 1.5 Respect Safe Areas

iPad safe areas differ from iPhone. Older iPads have no home indicator. iPads in landscape have different insets than portrait. Always use `safeAreaInset` and never hardcode padding for notches or indicators.

### 1.6 Support Both Orientations

iPad apps must work well in both portrait and landscape. Landscape is the dominant orientation for productivity. Portrait is common for reading. Adapt column counts and layout density to orientation.

---

## 2. Multitasking (CRITICAL)

### 2.1 Support Split View

Your app must function correctly at 1/3, 1/2, and 2/3 screen widths in Split View. At 1/3 width, your app receives compact horizontal size class. Content must remain usable at every split ratio.

### 2.2 Support Slide Over

Slide Over presents your app as a compact-width overlay on the right edge. It behaves like an iPhone-width app. Ensure all functionality remains accessible in this narrow mode.

### 2.3 Handle Stage Manager

Stage Manager allows freely resizable windows and multiple windows simultaneously. Your app must:
- Resize fluidly to arbitrary dimensions
- Support multiple scenes (windows) showing different content
- Not assume any fixed size or aspect ratio

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        // Support multiple windows
        WindowGroup("Detail", for: Item.ID.self) { $itemId in
            DetailView(itemId: itemId)
        }
    }
}
```

### 2.4 Never Assume Full Screen

The app may launch directly into Split View or Stage Manager. Do not depend on full-screen dimensions during setup, onboarding, or any flow. Test your app at every possible size.

### 2.5 Handle Size Transitions Gracefully

When the user resizes via multitasking, animate layout changes smoothly. Preserve scroll position, selection state, and user context across size transitions. Never reload content on resize.

### 2.6 Support Multiple Scenes

Use `UIScene` / SwiftUI `WindowGroup` to let users open multiple instances of your app showing different content. Each scene is independent. Support `NSUserActivity` for state restoration.

---

## 3. Navigation (HIGH)

### 3.1 Sidebar for Primary Navigation

In regular width, replace the iPhone tab bar with a sidebar. The sidebar provides more room for navigation items, supports sections, and feels native on iPad.

```swift
struct AppNavigation: View {
    @State private var selection: NavigationItem? = .inbox

    var body: some View {
        NavigationSplitView {
            List(selection: $selection) {
                Section("Main") {
                    Label("Inbox", systemImage: "tray")
                        .tag(NavigationItem.inbox)
                    Label("Drafts", systemImage: "doc")
                        .tag(NavigationItem.drafts)
                    Label("Sent", systemImage: "paperplane")
                        .tag(NavigationItem.sent)
                }
                Section("Labels") {
                    // Dynamic sections
                }
            }
            .navigationTitle("Mail")
        } detail: {
            DetailView(for: selection)
        }
    }
}
```

### 3.2 Automatic Tab-to-Sidebar Conversion

SwiftUI `TabView` with `.sidebarAdaptable` style automatically converts to a sidebar in regular width. Use this for seamless iPhone-to-iPad adaptation.

```swift
TabView {
    Tab("Home", systemImage: "house") { HomeView() }
    Tab("Search", systemImage: "magnifyingglass") { SearchView() }
    Tab("Profile", systemImage: "person") { ProfileView() }
}
.tabViewStyle(.sidebarAdaptable)
```

### 3.3 Three-Column Layout for Complex Hierarchies

Use `NavigationSplitView` with three columns when your information architecture has three levels: category > list > detail. Examples: mail (accounts > messages > message), file managers, settings.

### 3.4 Toolbar at Top

On iPad, toolbars live at the top of the screen in the navigation bar area, not at the bottom like iPhone. Place contextual actions in `.toolbar` with appropriate placement.

```swift
.toolbar {
    ToolbarItemGroup(placement: .primaryAction) {
        Button("Compose", systemImage: "square.and.pencil") { }
    }
    ToolbarItemGroup(placement: .secondaryAction) {
        Button("Archive", systemImage: "archivebox") { }
        Button("Delete", systemImage: "trash") { }
    }
}
```

### 3.5 Detail View Should Never Be Empty

When no item is selected in a list/sidebar, show a meaningful empty state in the detail area. Use a placeholder with icon and instruction text, not a blank screen.

---

## 4. Pointer & Trackpad (HIGH)

### 4.1 Add Hover Effects to Interactive Elements

All tappable elements should respond to pointer hover. The system provides automatic hover effects for standard controls. For custom views, use `.hoverEffect()`.

```swift
Button("Action") { }
    .hoverEffect(.highlight)  // Subtle highlight on hover

// Custom hover effect
MyCustomView()
    .hoverEffect(.lift)  // Lifts and adds shadow
```

### 4.2 Pointer Magnetism on Buttons

The pointer should snap to (be attracted toward) button bounds. Standard UIKit/SwiftUI buttons get this automatically. For custom hit targets, ensure the pointer region matches the tappable area using `.contentShape()`.

### 4.3 Support Right-Click Context Menus

Right-click (secondary click) should present context menus. Use `.contextMenu` which automatically supports both long-press (touch) and right-click (pointer).

```swift
Text(item.title)
    .contextMenu {
        Button("Copy", systemImage: "doc.on.doc") { }
        Button("Share", systemImage: "square.and.arrow.up") { }
        Divider()
        Button("Delete", systemImage: "trash", role: .destructive) { }
    }
```

### 4.4 Trackpad Scroll Behaviors

Support two-finger scrolling with momentum. Pinch to zoom where appropriate. Respect scroll direction preferences. For custom scroll views, ensure trackpad gestures feel natural alongside touch gestures.

### 4.5 Customize Cursor for Content Areas

Change cursor appearance based on context. Text areas show I-beam. Links show pointer hand. Resize handles show resize cursors. Draggable items show grab cursor.

### 4.6 Pointer-Driven Drag and Drop

Pointer users expect click-and-drag for rearranging, selecting, and moving content. Combine with multi-select via Shift-click and Cmd-click.

---

## 5. Keyboard (HIGH)

### 5.1 Cmd+Key Shortcuts for All Major Actions

Every primary action must have a keyboard shortcut. Standard shortcuts are mandatory:

| Shortcut | Action |
|----------|--------|
| Cmd+N | New item |
| Cmd+F | Find/Search |
| Cmd+S | Save |
| Cmd+Z | Undo |
| Cmd+Shift+Z | Redo |
| Cmd+C/V/X | Copy/Paste/Cut |
| Cmd+A | Select all |
| Cmd+P | Print |
| Cmd+W | Close window/tab |
| Cmd+, | Settings/Preferences |
| Delete | Delete selected item |

```swift
Button("New Document") { createDocument() }
    .keyboardShortcut("n", modifiers: .command)
```

### 5.2 Discoverability via Cmd-Hold Overlay

When the user holds the Cmd key, iPadOS shows a shortcut overlay. Register all shortcuts using `.keyboardShortcut()` so they appear in this overlay. Group related shortcuts logically.

### 5.3 Tab Key Navigation Between Fields

Support Tab to move forward and Shift+Tab to move backward between form fields and focusable elements. Use `.focusable()` and `@FocusState` to manage keyboard focus order.

```swift
struct FormView: View {
    @FocusState private var focusedField: Field?

    var body: some View {
        Form {
            TextField("Name", text: $name)
                .focused($focusedField, equals: .name)
            TextField("Email", text: $email)
                .focused($focusedField, equals: .email)
            TextField("Phone", text: $phone)
                .focused($focusedField, equals: .phone)
        }
    }
}
```

### 5.4 Never Override System Shortcuts

Do not claim shortcuts reserved by the system: Cmd+H (Home), Cmd+Tab (App Switcher), Cmd+Space (Spotlight), Globe key combinations. These will not work and create confusion.

### 5.5 Detect Hardware Keyboard

Adapt UI when a hardware keyboard is connected. Hide the on-screen keyboard shortcut bar. Show keyboard-optimized controls. Use `GCKeyboard` or track keyboard visibility to detect state.

### 5.6 Arrow Key Navigation

Support arrow keys for navigating lists, grids, and collections. Combine with Shift for multi-selection. This is essential for productivity-focused apps.

---

## 6. Apple Pencil (MEDIUM)

### 6.1 Support Scribble

iPadOS converts handwriting to text in any standard text field automatically. Do not disable Scribble. For custom text input, adopt `UIScribbleInteraction`. Test that Scribble works in all text entry points.

### 6.2 Double-Tap Tool Switching

Apple Pencil 2 and later supports double-tap to switch tools (e.g., pen to eraser). If your app has drawing tools, implement the `UIPencilInteraction` delegate to handle double-tap.

### 6.3 Pressure and Tilt for Drawing

For drawing apps, respond to `force` (pressure) and `altitudeAngle`/`azimuthAngle` (tilt) from pencil touch events. Use these for variable line width, opacity, or shading.

### 6.4 Hover Detection (M2+ Pencil)

Apple Pencil with hover (M2 iPad Pro and later) provides position data before the pencil touches the screen. Use this for preview effects, tool size indicators, and enhanced precision.

```swift
// UIKit hover support
override func pencilHoverChanged(_ hover: UIHoverGestureRecognizer) {
    let location = hover.location(in: canvas)
    showBrushPreview(at: location)
}
```

### 6.5 PencilKit Integration

For note-taking and annotation, use `PKCanvasView` from PencilKit. It provides a full drawing experience with tool picker, undo, and ink recognition out of the box.

```swift
import PencilKit

struct DrawingView: UIViewRepresentable {
    @Binding var canvasView: PKCanvasView

    func makeUIView(context: Context) -> PKCanvasView {
        canvasView.tool = PKInkingTool(.pen, color: .black, width: 5)
        canvasView.drawingPolicy = .anyInput
        return canvasView
    }
}
```

---

## 7. Drag and Drop (HIGH)

### 7.1 Inter-App Drag and Drop is Expected

iPad users expect to drag content between apps. Support dragging content out (as a source) and dropping content in (as a destination). This is a core iPad interaction.

```swift
// As drag source
Text(item.title)
    .draggable(item.title)

// As drop destination
DropTarget()
    .dropDestination(for: String.self) { items, location in
        handleDrop(items)
        return true
    }
```

### 7.2 Multi-Item Drag

Users can pick up one item, then tap additional items to add them to the drag. Support multi-item drag by providing multiple `NSItemProvider` items. Show a badge count on the drag preview.

### 7.3 Spring-Loaded Interactions

When dragging over a navigation element (folder, tab, sidebar item), pause briefly to "spring open" that destination. Implement spring-loading on navigation containers to enable deep drop targets.

### 7.4 Visual Feedback for Drag and Drop

Provide clear visual states:
- **Lift**: Item lifts with shadow when drag begins
- **Move**: Destination highlights when drag hovers over valid target
- **Drop**: Animate insertion at drop point
- **Cancel**: Item animates back to origin

### 7.5 Support Universal Control

Universal Control lets users drag between iPad and Mac. If your app supports drag and drop with standard `NSItemProvider` and UTTypes, Universal Control works automatically.

### 7.6 Drop Delegates for Custom Behavior

Use `DropDelegate` for fine-grained control over drop behavior: validating drop content, reordering within lists, and handling drop position.

```swift
struct ReorderDropDelegate: DropDelegate {
    let item: Item
    @Binding var items: [Item]
    @Binding var draggedItem: Item?

    func performDrop(info: DropInfo) -> Bool {
        draggedItem = nil
        return true
    }

    func dropEntered(info: DropInfo) {
        guard let draggedItem,
              let fromIndex = items.firstIndex(of: draggedItem),
              let toIndex = items.firstIndex(of: item) else { return }
        withAnimation {
            items.move(fromOffsets: IndexSet(integer: fromIndex),
                      toOffset: toIndex > fromIndex ? toIndex + 1 : toIndex)
        }
    }
}
```

---

## 8. External Display (MEDIUM)

### 8.1 Provide Extended Content, Not Just Mirroring

When connected to an external display, show complementary content rather than duplicating the iPad screen. Presentations, reference material, or expanded views belong on the external display while controls stay on iPad.

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        // Additional scene for external display
        WindowGroup(id: "presentation") {
            PresentationView()
        }
    }
}
```

### 8.2 Handle Display Connection and Disconnection

Listen for `UIScreen.didConnectNotification` and `UIScreen.didDisconnectNotification`. Transition gracefully -- if the external display disconnects mid-presentation, bring content back to the iPad screen without data loss.

### 8.3 Support Full External Display Resolution

Use the full resolution and aspect ratio of the external display. Do not letterbox or pillarbox your content. Query `UIScreen.bounds` and `UIScreen.scale` for the connected display.

---

## Evaluation Checklist

Use this checklist to verify iPad-readiness:

### Layout & Multitasking
- [ ] App uses adaptive layout with `horizontalSizeClass`
- [ ] Tested at all Split View ratios (1/3, 1/2, 2/3)
- [ ] Tested in Slide Over (compact width)
- [ ] Stage Manager: resizes fluidly to arbitrary dimensions
- [ ] Multiple scenes/windows supported
- [ ] Both orientations (portrait and landscape) work correctly
- [ ] No content clipped at any size
- [ ] Safe areas respected on all iPad models

### Navigation
- [ ] Sidebar visible in regular width
- [ ] Tab bar used in compact width
- [ ] Detail view shows placeholder when no selection
- [ ] Toolbar items placed at top, not bottom
- [ ] Three-column layout used where appropriate

### Pointer & Trackpad
- [ ] Hover effects on all interactive elements
- [ ] Right-click context menus available
- [ ] Pointer cursor adapts to content (I-beam for text, etc.)
- [ ] Click-and-drag works for reordering

### Keyboard
- [ ] Cmd+key shortcuts for all major actions
- [ ] Shortcuts appear in Cmd-hold overlay
- [ ] Tab key navigates between form fields
- [ ] No system shortcut conflicts
- [ ] Arrow keys navigate lists and grids
- [ ] Return/Enter activates default action

### Apple Pencil
- [ ] Scribble works in all text fields
- [ ] Drawing apps support pressure and tilt
- [ ] Double-tap interaction handled (if applicable)

### Drag and Drop
- [ ] Content can be dragged out to other apps
- [ ] Content can be dropped in from other apps
- [ ] Multi-item drag supported
- [ ] Visual feedback for all drag states

### External Display
- [ ] Extended content shown (not just mirror)
- [ ] Graceful handling of connect/disconnect

---

## Anti-Patterns

### DO NOT: Scale Up iPhone Layouts
Stretching a single-column iPhone UI to fill an iPad screen wastes space, looks lazy, and provides a poor experience. Always redesign for the larger canvas.

### DO NOT: Disable Multitasking
Never opt out of multitasking support. Users expect every app to work in Split View and Slide Over. Requiring full screen is hostile to iPad workflows.

### DO NOT: Ignore the Keyboard
Many iPad users have Magic Keyboard or Smart Keyboard. An app with no keyboard shortcuts forces them to reach for the screen constantly. Provide shortcuts for all frequent actions.

### DO NOT: Use iPhone-Style Bottom Tab Bars in Regular Width
Tab bars at the bottom waste vertical space on iPad and look out of place. Convert to sidebar navigation in regular width. SwiftUI does this automatically with `.sidebarAdaptable`.

### DO NOT: Show Popovers as Full-Screen Sheets
On iPad, popovers should anchor to their source element as floating panels. Only use full-screen sheets for immersive content or flows that genuinely need the full screen. Avoid the iPhone pattern of everything being a sheet.

### DO NOT: Ignore Pointer Hover States
Missing hover effects make the app feel broken when using a trackpad. Users cannot tell what is interactive. Always add hover feedback to custom interactive elements.

### DO NOT: Hardcode Dimensions
Never hardcode widths, heights, or positions based on a specific iPad model. Use Auto Layout constraints, SwiftUI flexible frames, and `GeometryReader` for dynamic sizing.

### DO NOT: Forget Drag and Drop
On iPad, drag and drop between apps is a core workflow. Not supporting it makes your app a dead end for content. At minimum, support dragging text, images, and URLs in and out.

### DO NOT: Override System Keyboard Shortcuts
Claiming Cmd+H, Cmd+Tab, Cmd+Space, or Globe shortcuts will not work and confuses users who expect system behavior. Check Apple's reserved shortcuts list before assigning.

### DO NOT: Present Dense Content Without Scrolling
Large iPad screens tempt designers to show everything at once. Content should still scroll when it exceeds the visible area. Never truncate content to avoid scrolling.
