---
name: ipados-design-guidelines
description: Apple Human Interface Guidelines for iPad. Use when building iPad-optimized interfaces, implementing multitasking, pointer support, keyboard shortcuts, or responsive layouts. Triggers on tasks involving iPad, Split View, Stage Manager, sidebar navigation, or trackpad support.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# iPadOS Design Guidelines

Rules for building iPad-native apps. These extend iOS patterns for adaptive layouts, multitasking, pointer interactions, keyboard shortcuts, and inter-app drag and drop.

---

## 1. Responsive Layout (CRITICAL)

### 1.1 Use Adaptive Size Classes

Design for both `regular` and `compact` horizontal size classes. Never hardcode dimensions.

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

Use multi-column layouts, master-detail patterns, and increased information density in regular width.

### 1.3 Support All iPad Screen Sizes

Use flexible layouts that redistribute content across iPad Mini through iPad Pro rather than simply scaling.

### 1.4 Column-Based Layouts for Regular Width

Use two-column (sidebar + detail) or three-column (sidebar + list + detail) layouts in regular width. Avoid single-column full-width layouts.

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

Always use `safeAreaInset` and never hardcode padding for notches or indicators.

### 1.6 Support Both Orientations

Adapt column counts and layout density to orientation. Support both portrait and landscape.

---

## 2. Multitasking (CRITICAL)

### 2.1 Support Split View

App must function correctly at 1/3, 1/2, and 2/3 screen widths. Content must remain usable at every split ratio.

### 2.2 Support Slide Over

Ensure all functionality remains accessible in compact-width Slide Over mode.

### 2.3 Handle Stage Manager

Your app must:
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

Do not depend on full-screen dimensions during setup, onboarding, or any flow. Test at every possible size.

### 2.5 Handle Size Transitions Gracefully

Animate layout changes on resize. Preserve scroll position, selection state, and user context. Never reload content on resize.

### 2.6 Support Multiple Scenes

Use `UIScene` / SwiftUI `WindowGroup` for multiple independent windows. Support `NSUserActivity` for state restoration.

---

## 3. Navigation (HIGH)

### 3.1 Sidebar for Primary Navigation

In regular width, replace the iPhone tab bar with a sidebar.

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

Use `TabView` with `.sidebarAdaptable` for automatic sidebar conversion in regular width.

```swift
TabView {
    Tab("Home", systemImage: "house") { HomeView() }
    Tab("Search", systemImage: "magnifyingglass") { SearchView() }
    Tab("Profile", systemImage: "person") { ProfileView() }
}
.tabViewStyle(.sidebarAdaptable)
```

### 3.3 Three-Column Layout for Complex Hierarchies

Use `NavigationSplitView` with three columns for category > list > detail hierarchies.

### 3.4 Toolbar at Top

Place contextual actions in `.toolbar` at the top, not at the bottom like iPhone.

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

Show a placeholder with icon and instruction text when no item is selected -- never a blank screen.

---

## 4. Pointer & Trackpad (HIGH)

### 4.1 Add Hover Effects to Interactive Elements

All tappable elements should respond to pointer hover. For custom views, use `.hoverEffect()`.

```swift
Button("Action") { }
    .hoverEffect(.highlight)  // Subtle highlight on hover

// Custom hover effect
MyCustomView()
    .hoverEffect(.lift)  // Lifts and adds shadow
```

### 4.2 Pointer Magnetism on Buttons

For custom hit targets, ensure the pointer region matches the tappable area using `.contentShape()`.

### 4.3 Support Right-Click Context Menus

Use `.contextMenu` which supports both long-press (touch) and right-click (pointer).

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

Support two-finger scrolling with momentum and pinch to zoom. For custom scroll views, ensure trackpad gestures feel natural alongside touch gestures.

### 4.5 Customize Cursor for Content Areas

Set cursor to I-beam for text, pointer hand for links, resize cursors for handles, grab cursor for draggable items.

### 4.6 Pointer-Driven Drag and Drop

Support click-and-drag for rearranging and moving content. Combine with Shift-click and Cmd-click for multi-select.

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

Register all shortcuts using `.keyboardShortcut()` so they appear in the Cmd-hold overlay. Group related shortcuts logically.

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

Do not claim Cmd+H, Cmd+Tab, Cmd+Space, or Globe key combinations -- these are reserved by the system.

### 5.5 Detect Hardware Keyboard

Adapt UI when hardware keyboard is connected. Use `GCKeyboard` or track keyboard visibility to detect state.

### 5.6 Arrow Key Navigation

Support arrow keys for navigating lists, grids, and collections. Combine with Shift for multi-selection.

---

## 6. Apple Pencil (MEDIUM)

### 6.1 Support Scribble

Do not disable Scribble. For custom text input, adopt `UIScribbleInteraction`. Test all text entry points.

### 6.2 Double-Tap Tool Switching

For drawing tools, implement the `UIPencilInteraction` delegate to handle Apple Pencil double-tap tool switching.

### 6.3 Pressure and Tilt for Drawing

For drawing apps, respond to `force` (pressure) and `altitudeAngle`/`azimuthAngle` (tilt) from pencil touch events. Use these for variable line width, opacity, or shading.

### 6.4 Hover Detection (M2+ Pencil)

Use hover position data for preview effects, tool size indicators, and enhanced precision.

```swift
// UIKit hover support
override func pencilHoverChanged(_ hover: UIHoverGestureRecognizer) {
    let location = hover.location(in: canvas)
    showBrushPreview(at: location)
}
```

### 6.5 PencilKit Integration

Use `PKCanvasView` from PencilKit for note-taking and annotation.

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

Support dragging content out (as a source) and dropping content in (as a destination).

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

Support multi-item drag via multiple `NSItemProvider` items. Show a badge count on the drag preview.

### 7.3 Spring-Loaded Interactions

Implement spring-loading on navigation containers so dragging over a folder/tab/sidebar item opens it as a drop target.

### 7.4 Visual Feedback for Drag and Drop

Provide clear visual states:
- **Lift**: Item lifts with shadow when drag begins
- **Move**: Destination highlights when drag hovers over valid target
- **Drop**: Animate insertion at drop point
- **Cancel**: Item animates back to origin

### 7.5 Support Universal Control

Use standard `NSItemProvider` and UTTypes -- Universal Control works automatically when these are implemented.

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

Show complementary content on the external display while controls stay on iPad.

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

Listen for `UIScreen.didConnectNotification` and `UIScreen.didDisconnectNotification`. Bring content back to iPad on disconnect without data loss.

### 8.3 Support Full External Display Resolution

Use the full resolution and aspect ratio via `UIScreen.bounds` and `UIScreen.scale`. Do not letterbox or pillarbox.

---

## Implementation Workflow

Step-by-step process for adding multitasking support to an iPad app:

1. **Set up adaptive layout**: Add `@Environment(\.horizontalSizeClass)` and branch between regular (multi-column) and compact (stacked) layouts.
2. **Enable Split View and Slide Over**: Verify `UIRequiresFullScreen` is `NO` in Info.plist. Test at 1/3, 1/2, and 2/3 widths.
3. **Add multiple window support**: Use `WindowGroup` in your `App` struct. Support `NSUserActivity` for state restoration per scene.
4. **Validate Stage Manager**: Test arbitrary resize dimensions. Confirm no fixed-size assumptions.
5. **Add sidebar navigation**: Use `NavigationSplitView` or `TabView` with `.sidebarAdaptable`. Provide a detail placeholder for empty selection.
6. **Implement keyboard shortcuts**: Add `.keyboardShortcut()` for all primary actions (see Section 5.1). Verify they appear in Cmd-hold overlay.
7. **Add pointer support**: Apply `.hoverEffect()` to custom interactive elements. Add `.contextMenu` for right-click.
8. **Enable drag and drop**: Implement `.draggable()` and `.dropDestination()` for content types. Test inter-app drag.
9. **Test all configurations**: Run through the [evaluation checklist](./REFERENCE.md) across orientations, split ratios, and input methods.

---

## Evaluation Checklist & Anti-Patterns

See [REFERENCE.md](./REFERENCE.md) for the full iPad-readiness checklist and anti-patterns list.
