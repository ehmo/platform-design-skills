---
name: macos-design-guidelines
description: Apple Human Interface Guidelines for Mac. Use when building macOS apps with SwiftUI or AppKit, implementing menu bars, toolbars, window management, or keyboard shortcuts. Triggers on tasks involving Mac UI, desktop apps, or Mac Catalyst.
license: MIT
metadata:
  author: platform-design-skills
  version: "1.0.0"
---

# macOS Human Interface Guidelines

Mac apps serve power users who expect deep keyboard control, persistent menu bars, resizable multi-window layouts, and tight system integration. These guidelines codify Apple's HIG into actionable rules with SwiftUI and AppKit examples.

---

## 1. Menu Bar (CRITICAL)

Every Mac app must have a menu bar. It is the primary discovery mechanism for commands. Users who cannot find a feature will look in the menu bar before anywhere else.

### Rule 1.1 — Provide Standard Menus

Every app must include at minimum: **App**, **File**, **Edit**, **View**, **Window**, **Help**. Omit File only if the app is not document-based. Add app-specific menus between Edit and View or between View and Window.

```swift
// SwiftUI — Standard menu structure
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .commands {
            // Adds to existing standard menus
            CommandGroup(after: .newItem) {
                Button("New from Template...") { newFromTemplate() }
                    .keyboardShortcut("T", modifiers: [.command, .shift])
            }
            CommandMenu("Canvas") {
                Button("Zoom to Fit") { zoomToFit() }
                    .keyboardShortcut("0", modifiers: .command)
                Divider()
                Button("Add Artboard") { addArtboard() }
                    .keyboardShortcut("A", modifiers: [.command, .shift])
            }
        }
    }
}
```

```swift
// AppKit — Building menus programmatically
let editMenu = NSMenu(title: "Edit")
let undoItem = NSMenuItem(title: "Undo", action: #selector(UndoManager.undo), keyEquivalent: "z")
let redoItem = NSMenuItem(title: "Redo", action: #selector(UndoManager.redo), keyEquivalent: "Z")
editMenu.addItem(undoItem)
editMenu.addItem(redoItem)
editMenu.addItem(.separator())
```

### Rule 1.2 — Keyboard Shortcuts for All Menu Items

Every menu item that performs an action must have a keyboard shortcut. Use standard shortcuts for standard actions (Cmd+C, Cmd+V, Cmd+Z, etc.). Custom shortcuts should use Cmd plus a letter. Reserve Cmd+Shift, Cmd+Option, and Cmd+Ctrl combos for secondary actions.

**Standard Shortcut Reference:**

| Action | Shortcut |
|--------|----------|
| New | Cmd+N |
| Open | Cmd+O |
| Close | Cmd+W |
| Save | Cmd+S |
| Save As | Cmd+Shift+S |
| Print | Cmd+P |
| Undo | Cmd+Z |
| Redo | Cmd+Shift+Z |
| Cut | Cmd+X |
| Copy | Cmd+C |
| Paste | Cmd+V |
| Select All | Cmd+A |
| Find | Cmd+F |
| Find Next | Cmd+G |
| Preferences/Settings | Cmd+, |
| Hide App | Cmd+H |
| Quit | Cmd+Q |
| Minimize | Cmd+M |
| Fullscreen | Cmd+Ctrl+F |

### Rule 1.3 — Dynamic Menu Updates

Menu items must reflect current state. Disable items that are not applicable. Update titles to match context (e.g., "Undo Typing" not just "Undo"). Toggle checkmarks for on/off states.

```swift
// SwiftUI — Dynamic menu state
CommandGroup(replacing: .toolbar) {
    Button(showingSidebar ? "Hide Sidebar" : "Show Sidebar") {
        showingSidebar.toggle()
    }
    .keyboardShortcut("S", modifiers: [.command, .control])
}
```

```swift
// AppKit — Validate menu items
override func validateMenuItem(_ menuItem: NSMenuItem) -> Bool {
    if menuItem.action == #selector(delete(_:)) {
        menuItem.title = selectedItems.count > 1 ? "Delete \(selectedItems.count) Items" : "Delete"
        return !selectedItems.isEmpty
    }
    return super.validateMenuItem(menuItem)
}
```

### Rule 1.4 — Contextual Menus

Provide right-click context menus on all interactive elements. Context menus should contain the most relevant subset of menu bar actions for the clicked element, plus element-specific actions.

```swift
// SwiftUI
Text(item.name)
    .contextMenu {
        Button("Rename...") { rename(item) }
        Button("Duplicate") { duplicate(item) }
        Divider()
        Button("Delete", role: .destructive) { delete(item) }
    }
```

### Rule 1.5 — App Menu Structure

The App menu (leftmost, bold app name) must contain: About, Preferences/Settings (Cmd+,), Services submenu, Hide App (Cmd+H), Hide Others (Cmd+Option+H), Show All, Quit (Cmd+Q). Never rename or remove these standard items.

```swift
// SwiftUI — Settings scene
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup { ContentView() }
        Settings { SettingsView() }  // Automatically wired to Cmd+,
    }
}
```

---

## 2. Windows (CRITICAL)

Mac users expect full control over window size, position, and lifecycle. An app that fights window management feels fundamentally broken on the Mac.

### Rule 2.1 — Resizable with Sensible Minimums

All main windows must be freely resizable. Set a minimum size that keeps the UI usable. Never set a maximum size unless the content truly cannot scale (rare).

```swift
// SwiftUI
WindowGroup {
    ContentView()
        .frame(minWidth: 600, minHeight: 400)
}
.defaultSize(width: 900, height: 600)
```

```swift
// AppKit
window.minSize = NSSize(width: 600, height: 400)
window.setContentSize(NSSize(width: 900, height: 600))
```

### Rule 2.2 — Support Fullscreen and Split View

Opt into native fullscreen by setting the appropriate window collection behavior. The green traffic-light button must either enter fullscreen or show the tile picker.

```swift
// AppKit
window.collectionBehavior.insert(.fullScreenPrimary)
```

SwiftUI windows get fullscreen support automatically.

### Rule 2.3 — Multiple Windows

Unless your app is a single-purpose utility, support multiple windows. Document-based apps must allow multiple documents open simultaneously. Use `WindowGroup` or `DocumentGroup` in SwiftUI.

```swift
// SwiftUI — Document-based app
@main
struct TextEditorApp: App {
    var body: some Scene {
        DocumentGroup(newDocument: TextDocument()) { file in
            TextEditorView(document: file.$document)
        }
    }
}
```

### Rule 2.4 — Title Bar Shows Document Info

For document-based apps, the title bar must show the document name. Support proxy icon dragging. Show edited state (dot in close button). Support title bar renaming on click.

```swift
// AppKit
window.representedURL = document.fileURL
window.title = document.displayName
window.isDocumentEdited = document.hasUnsavedChanges
```

```swift
// SwiftUI — NavigationSplitView titles
NavigationSplitView {
    SidebarView()
} detail: {
    DetailView()
        .navigationTitle(document.name)
}
```

### Rule 2.5 — Remember Window State

Persist window position, size, and state across launches. Use `NSWindow.setFrameAutosaveName` or SwiftUI's built-in state restoration.

```swift
// AppKit
window.setFrameAutosaveName("MainWindow")

// SwiftUI — Automatic with WindowGroup
WindowGroup(id: "main") {
    ContentView()
}
.defaultPosition(.center)
```

### Rule 2.6 — Traffic Light Buttons

Never hide or reposition the close (red), minimize (yellow), or zoom (green) buttons. They must remain in the top-left corner. If using a custom title bar, the buttons must still be visible and functional.

```swift
// AppKit — Custom title bar that preserves traffic lights
window.titlebarAppearsTransparent = true
window.styleMask.insert(.fullSizeContentView)
// Traffic lights remain functional and visible
```

---

## 3. Toolbars (HIGH)

Toolbars are the secondary command surface after the menu bar. They provide quick access to frequent actions and should be customizable.

### Rule 3.1 — Unified Title Bar and Toolbar

Use the unified title bar + toolbar style for a modern appearance. The toolbar sits in the title bar area, saving vertical space.

```swift
// SwiftUI
WindowGroup {
    ContentView()
        .toolbar {
            ToolbarItem(placement: .primaryAction) {
                Button(action: compose) {
                    Label("Compose", systemImage: "square.and.pencil")
                }
            }
        }
}
.windowToolbarStyle(.unified)
```

```swift
// AppKit
window.titleVisibility = .hidden
window.toolbarStyle = .unified
```

### Rule 3.2 — User-Customizable Toolbars

Allow users to add, remove, and rearrange toolbar items. Provide a default set and a superset of available items.

```swift
// SwiftUI — Customizable toolbar
.toolbar(id: "main") {
    ToolbarItem(id: "compose", placement: .primaryAction) {
        Button(action: compose) {
            Label("Compose", systemImage: "square.and.pencil")
        }
    }
    ToolbarItem(id: "filter", placement: .secondaryAction) {
        Button(action: toggleFilter) {
            Label("Filter", systemImage: "line.3.horizontal.decrease")
        }
    }
}
.toolbarRole(.editor)
```

### Rule 3.3 — Segmented Controls for View Switching

Use a segmented control or picker in the toolbar for switching between content views (e.g., List/Grid/Column). This is a toolbar pattern, not a tab bar.

```swift
// SwiftUI
ToolbarItem(placement: .principal) {
    Picker("View Mode", selection: $viewMode) {
        Label("List", systemImage: "list.bullet").tag(ViewMode.list)
        Label("Grid", systemImage: "square.grid.2x2").tag(ViewMode.grid)
        Label("Column", systemImage: "rectangle.split.3x1").tag(ViewMode.column)
    }
    .pickerStyle(.segmented)
}
```

### Rule 3.4 — Search Field in Toolbar

Place the search field in the trailing area of the toolbar. Use `.searchable()` in SwiftUI for standard search behavior with suggestions and tokens.

```swift
// SwiftUI
NavigationSplitView {
    SidebarView()
} detail: {
    ContentListView()
        .searchable(text: $searchText, placement: .toolbar, prompt: "Search items")
        .searchSuggestions {
            ForEach(suggestions) { suggestion in
                Text(suggestion.title).searchCompletion(suggestion.title)
            }
        }
}
```

### Rule 3.5 — Toolbar Labels and Icons

Toolbar items should have both an icon (SF Symbol) and a text label. In compact mode, show icons only. Prefer labeled icons for discoverability. Use `Label` to supply both.

---

## 4. Sidebars (HIGH)

Sidebars are the primary navigation surface for Mac apps. They appear on the leading edge and provide persistent access to top-level sections and content libraries.

### Rule 4.1 — Leading Edge, Collapsible

Place the sidebar on the left (leading) edge. Make it collapsible via the toolbar button or Cmd+Ctrl+S. Persist collapsed state.

```swift
// SwiftUI
NavigationSplitView(columnVisibility: $columnVisibility) {
    List(selection: $selection) {
        Section("Library") {
            Label("All Items", systemImage: "tray.full")
            Label("Favorites", systemImage: "star")
            Label("Recent", systemImage: "clock")
        }
        Section("Tags") {
            ForEach(tags) { tag in
                Label(tag.name, systemImage: "tag")
            }
        }
    }
    .navigationSplitViewColumnWidth(min: 180, ideal: 220, max: 320)
} detail: {
    DetailView(selection: selection)
}
.navigationSplitViewStyle(.prominentDetail)
```

### Rule 4.2 — Source List Style

Use the source list style (`.listStyle(.sidebar)`) for content-library navigation. Source lists have a translucent background that shows the desktop or window behind them with vibrancy effects.

```swift
// SwiftUI
List(selection: $selection) {
    ForEach(sections) { section in
        Section(section.name) {
            ForEach(section.items) { item in
                NavigationLink(value: item) {
                    Label(item.name, systemImage: item.icon)
                }
            }
        }
    }
}
.listStyle(.sidebar)
```

### Rule 4.3 — Outline Views for Hierarchies

When content is hierarchical (e.g., folder trees, project structures), use disclosure groups or outline views to let users expand and collapse levels.

```swift
// SwiftUI — Recursive outline
List(selection: $selection) {
    OutlineGroup(rootNodes, children: \.children) { node in
        Label(node.name, systemImage: node.icon)
    }
}
```

### Rule 4.4 — Drag to Reorder

Sidebar items that can be reordered (bookmarks, favorites, custom sections) must support drag-to-reorder. Implement `onMove` or `NSOutlineView` drag delegates.

```swift
// SwiftUI
ForEach(favorites) { item in
    Label(item.name, systemImage: item.icon)
}
.onMove { source, destination in
    favorites.move(fromOffsets: source, toOffset: destination)
}
```

### Rule 4.5 — Badge Counts

Show badge counts on sidebar items for unread counts, pending items, or notifications. Use the `.badge()` modifier.

```swift
// SwiftUI
Label("Inbox", systemImage: "tray")
    .badge(unreadCount)
```

---

## 5. Keyboard (CRITICAL)

Mac users rely on keyboard shortcuts more than any other platform. An app without comprehensive keyboard support is a broken Mac app.

### Rule 5.1 — Cmd Shortcuts for Everything

Every action reachable by mouse must have a keyboard equivalent. Primary actions use Cmd+letter. Secondary actions use Cmd+Shift or Cmd+Option. Tertiary actions use Cmd+Ctrl.

**Keyboard Shortcut Conventions:**

| Modifier Pattern | Usage |
|-----------------|-------|
| Cmd+letter | Primary actions (New, Open, Save, etc.) |
| Cmd+Shift+letter | Variant of primary (Save As, Find Previous) |
| Cmd+Option+letter | Alternative mode (Paste and Match Style) |
| Cmd+Ctrl+letter | Window/view controls (Fullscreen, Sidebar) |
| Ctrl+letter | Emacs-style text navigation (acceptable) |
| Fn+key | System functions (F11 Show Desktop, etc.) |

### Rule 5.2 — Full Keyboard Navigation

Support Tab to move between controls. Support arrow keys within lists, grids, and tables. Support Shift+Tab for reverse navigation. Use `focusable()` and `@FocusState` in SwiftUI.

```swift
// SwiftUI — Focus management
struct ContentView: View {
    @FocusState private var focusedField: Field?

    var body: some View {
        VStack {
            TextField("Name", text: $name)
                .focused($focusedField, equals: .name)
            TextField("Email", text: $email)
                .focused($focusedField, equals: .email)
        }
        .onSubmit { advanceFocus() }
    }
}
```

### Rule 5.3 — Escape to Cancel or Close

Esc must dismiss popovers, sheets, dialogs, and cancel in-progress operations. In text fields, Esc reverts to the previous value. In modal dialogs, Esc is equivalent to clicking Cancel.

```swift
// SwiftUI — Sheet with Esc support (automatic)
.sheet(isPresented: $showingSheet) {
    SheetView()  // Esc dismisses automatically
}

// AppKit — Custom responder
override func cancelOperation(_ sender: Any?) {
    dismiss(nil)
}
```

### Rule 5.4 — Return for Default Action

In dialogs and forms, Return/Enter activates the default button (visually emphasized in blue). The default button is always the safest primary action.

```swift
// SwiftUI
Button("Save") { save() }
    .keyboardShortcut(.defaultAction)  // Enter key

Button("Cancel") { cancel() }
    .keyboardShortcut(.cancelAction)   // Esc key
```

### Rule 5.5 — Delete for Removal

The Delete key (Backspace) must remove selected items in lists, tables, and collections. Cmd+Delete for more destructive removal (move to Trash). Always support Cmd+Z to undo deletion.

### Rule 5.6 — Space for Quick Look

When items support previewing, Space bar should invoke Quick Look. Use the `QLPreviewPanel` API in AppKit or `.quickLookPreview()` in SwiftUI.

```swift
// SwiftUI
List(selection: $selection) {
    ForEach(files) { file in
        FileRow(file: file)
    }
}
.quickLookPreview($quickLookItem, in: files)
```

### Rule 5.7 — Arrow Key Navigation

In lists and grids, Up/Down arrow keys move selection. Left/Right collapse/expand disclosure groups or navigate columns. Cmd+Up goes to the beginning, Cmd+Down goes to the end.

---

## 6. Pointer and Mouse (HIGH)

Mac is a pointer-driven platform. Every interactive element must respond to hover, click, right-click, and drag.

### Rule 6.1 — Hover States

All interactive elements must have a visible hover state. Buttons highlight, rows show a selection indicator, links change cursor. Use `.onHover` in SwiftUI.

```swift
// SwiftUI — Hover effect
struct HoverableRow: View {
    @State private var isHovered = false

    var body: some View {
        HStack {
            Text(item.name)
            Spacer()
            if isHovered {
                Button("Edit") { edit() }
                    .buttonStyle(.borderless)
            }
        }
        .padding(8)
        .background(isHovered ? Color.primary.opacity(0.05) : .clear)
        .cornerRadius(6)
        .onHover { hovering in isHovered = hovering }
    }
}
```

### Rule 6.2 — Right-Click Context Menus

Every interactive element must respond to right-click with a contextual menu. The context menu should contain the most relevant actions for the clicked item.

### Rule 6.3 — Drag and Drop

Support drag and drop for content manipulation: reordering items, moving between containers, importing files from Finder, and exporting content.

```swift
// SwiftUI — Drag and drop
ForEach(items) { item in
    ItemView(item: item)
        .draggable(item)
}
.dropDestination(for: Item.self) { items, location in
    handleDrop(items, at: location)
    return true
}
```

```swift
// Accepting file drops from Finder
.dropDestination(for: URL.self) { urls, location in
    importFiles(urls)
    return true
}
```

### Rule 6.4 — Scroll Behavior

Support both trackpad (smooth/inertial) and mouse wheel (discrete) scrolling. Use elastic/bounce scrolling at content boundaries. Support horizontal scrolling where appropriate.

### Rule 6.5 — Cursor Changes

Change the cursor to indicate affordances: pointer for clickable elements, I-beam for text, crosshair for drawing, resize handles at window/splitter edges, grab hand for draggable content.

```swift
// AppKit — Custom cursor
override func resetCursorRects() {
    addCursorRect(bounds, cursor: .crosshair)
}
```

### Rule 6.6 — Multi-Selection

Support Cmd+Click for non-contiguous selection and Shift+Click for range selection in lists, tables, and grids. This is a deeply ingrained Mac interaction pattern.

```swift
// SwiftUI — Tables with multi-selection
Table(items, selection: $selectedItems) {
    TableColumn("Name", value: \.name)
    TableColumn("Date", value: \.dateFormatted)
    TableColumn("Size", value: \.sizeFormatted)
}
```

---

## 7. Notifications and Alerts (MEDIUM)

Mac users are protective of their attention. Only interrupt when truly necessary.

### Rule 7.1 — Use Notification Center Appropriately

Send notifications only for events that happen outside the app or require user action. Never notify for routine operations. Notifications must be actionable.

```swift
// UserNotifications
let content = UNMutableNotificationContent()
content.title = "Download Complete"
content.body = "project-assets.zip is ready"
content.categoryIdentifier = "DOWNLOAD"
content.sound = .default

let request = UNNotificationRequest(identifier: UUID().uuidString, content: content, trigger: nil)
UNUserNotificationCenter.current().add(request)
```

### Rule 7.2 — Alerts with Suppression Option

For recurring alerts, provide a "Do not show this again" checkbox. Respect the user's choice and persist it.

```swift
// AppKit — Alert with suppression
let alert = NSAlert()
alert.messageText = "Remove from library?"
alert.informativeText = "The file will be moved to the Trash."
alert.alertStyle = .warning
alert.addButton(withTitle: "Remove")
alert.addButton(withTitle: "Cancel")
alert.showsSuppressionButton = true
alert.suppressionButton?.title = "Do not ask again"

let response = alert.runModal()
if alert.suppressionButton?.state == .on {
    UserDefaults.standard.set(true, forKey: "suppressRemoveAlert")
}
```

### Rule 7.3 — Don't Interrupt Unnecessarily

Never show alerts for successful operations. Use inline status indicators, toolbar badges, or subtle animations instead. Reserve modal alerts for destructive or irreversible actions.

### Rule 7.4 — Dock Badge

Show a badge on the Dock icon for notification counts. Clear it promptly when the user addresses the notifications.

```swift
// AppKit
NSApp.dockTile.badgeLabel = unreadCount > 0 ? "\(unreadCount)" : nil
```

---

## 8. System Integration (MEDIUM)

Mac apps exist in a rich ecosystem. Deep integration makes an app feel native.

### Rule 8.1 — Dock Icon and Menus

Provide a high-quality 1024x1024 app icon. Support Dock right-click menus for quick actions. Show recent documents in the Dock menu.

```swift
// AppKit — Dock menu
override func applicationDockMenu(_ sender: NSApplication) -> NSMenu? {
    let menu = NSMenu()
    menu.addItem(withTitle: "New Window", action: #selector(newWindow(_:)), keyEquivalent: "")
    menu.addItem(withTitle: "New Document", action: #selector(newDocument(_:)), keyEquivalent: "")
    menu.addItem(.separator())
    for doc in recentDocuments.prefix(5) {
        menu.addItem(withTitle: doc.name, action: #selector(openRecent(_:)), keyEquivalent: "")
    }
    return menu
}
```

### Rule 8.2 — Spotlight Integration

Index app content for Spotlight search using `CSSearchableItem` and Core Spotlight. Users expect to find app content via Cmd+Space.

```swift
import CoreSpotlight

let attributeSet = CSSearchableItemAttributeSet(contentType: .text)
attributeSet.title = document.title
attributeSet.contentDescription = document.summary
attributeSet.thumbnailData = document.thumbnail?.pngData()

let item = CSSearchableItem(uniqueIdentifier: document.id, domainIdentifier: "documents", attributeSet: attributeSet)
CSSearchableIndex.default().indexSearchableItems([item])
```

### Rule 8.3 — Quick Look Support

Provide Quick Look previews for custom file types via a Quick Look Preview Extension. Users expect Space to preview any file in Finder.

### Rule 8.4 — Share Extensions

Implement the Share menu so users can share content from your app to Messages, Mail, Notes, etc. Also accept shared content from other apps.

```swift
// SwiftUI
ShareLink(item: document.url) {
    Label("Share", systemImage: "square.and.arrow.up")
}
```

### Rule 8.5 — Services Menu

Register for the Services menu to receive text, URLs, or files from other apps. This is a uniquely Mac integration point that power users rely on.

### Rule 8.6 — Shortcuts and AppleScript

Support the Shortcuts app by providing App Intents. For advanced automation, add AppleScript/JXA scripting support via an `.sdef` scripting dictionary.

```swift
// App Intents for Shortcuts
struct CreateDocumentIntent: AppIntent {
    static var title: LocalizedStringResource = "Create Document"
    static var description = IntentDescription("Creates a new document with the given title.")

    @Parameter(title: "Title")
    var title: String

    func perform() async throws -> some IntentResult {
        let doc = DocumentManager.shared.create(title: title)
        return .result(value: doc.title)
    }
}
```

---

## 9. Visual Design (HIGH)

Mac apps should look and feel like they belong on the platform. Use system-provided materials, fonts, and colors.

### Rule 9.1 — Use System Fonts

Use SF Pro (the system font) at standard dynamic type sizes. Use SF Mono for code. Never hardcode font sizes; use semantic styles.

```swift
// SwiftUI — Semantic font styles
Text("Title").font(.title)
Text("Headline").font(.headline)
Text("Body text").font(.body)
Text("Caption").font(.caption)
Text("let x = 42").font(.system(.body, design: .monospaced))
```

### Rule 9.2 — Vibrancy and Materials

Use system materials for sidebar and toolbar backgrounds. Vibrancy lets the desktop or underlying content show through, anchoring the app to the Mac visual language.

```swift
// SwiftUI
List { ... }
    .listStyle(.sidebar)  // Automatic vibrancy

// Custom vibrancy
ZStack {
    VisualEffectView(material: .sidebar, blendingMode: .behindWindow)
    Text("Sidebar Content")
}
```

```swift
// AppKit — Visual effect view
let visualEffect = NSVisualEffectView()
visualEffect.material = .sidebar
visualEffect.blendingMode = .behindWindow
visualEffect.state = .followsWindowActiveState
```

### Rule 9.3 — Respect System Accent Color

Use the system accent color for selection, emphasis, and interactive elements. Never override it with a fixed brand color for standard controls. Use `.accentColor` or `.tint` only on custom views when appropriate.

```swift
// SwiftUI — Follows system accent automatically
Button("Action") { doSomething() }
    .buttonStyle(.borderedProminent)  // Uses system accent color

Toggle("Enable feature", isOn: $isEnabled)  // Toggle tint follows accent
```

### Rule 9.4 — Support Dark Mode

Every view must support both Light and Dark appearances. Use semantic colors (`Color.primary`, `Color.secondary`, `.background`) rather than hardcoded colors. Test in both modes.

```swift
// SwiftUI — Semantic colors
Text("Title").foregroundStyle(.primary)
Text("Subtitle").foregroundStyle(.secondary)

RoundedRectangle(cornerRadius: 8)
    .fill(Color(nsColor: .controlBackgroundColor))

// Asset catalog: define colors for Both Appearances
// Never use Color.white or Color.black for UI surfaces
```

### Rule 9.5 — Translucency

Respect the "Reduce transparency" accessibility setting. When transparency is reduced, replace translucent materials with solid backgrounds.

```swift
// SwiftUI
@Environment(\.accessibilityReduceTransparency) var reduceTransparency

var body: some View {
    if reduceTransparency {
        Color(nsColor: .windowBackgroundColor)
    } else {
        VisualEffectView(material: .sidebar, blendingMode: .behindWindow)
    }
}
```

### Rule 9.6 — Consistent Spacing and Layout

Use 20pt standard margins, 8pt spacing between related controls, 20pt spacing between groups. Align controls to a grid. Use SwiftUI's built-in spacing or AppKit's Auto Layout with system spacing constraints.

---

## Keyboard Shortcut Quick Reference

### Navigation
| Shortcut | Action |
|----------|--------|
| Cmd+N | New window/document |
| Cmd+O | Open |
| Cmd+W | Close window/tab |
| Cmd+Q | Quit app |
| Cmd+, | Settings/Preferences |
| Cmd+Tab | Switch apps |
| Cmd+` | Switch windows within app |
| Cmd+T | New tab |

### Editing
| Shortcut | Action |
|----------|--------|
| Cmd+Z | Undo |
| Cmd+Shift+Z | Redo |
| Cmd+X / C / V | Cut / Copy / Paste |
| Cmd+A | Select All |
| Cmd+D | Duplicate |
| Cmd+F | Find |
| Cmd+G | Find Next |
| Cmd+Shift+G | Find Previous |
| Cmd+E | Use Selection for Find |

### View
| Shortcut | Action |
|----------|--------|
| Cmd+Ctrl+F | Toggle fullscreen |
| Cmd+Ctrl+S | Toggle sidebar |
| Cmd+0 | Show/hide toolbar |
| Cmd++ / Cmd+- | Zoom in/out |
| Cmd+0 | Actual size |

---

## Evaluation Checklist

Before shipping a Mac app, verify:

### Menu Bar
- [ ] App has a complete menu bar with standard menus
- [ ] All actions have keyboard shortcuts
- [ ] Menu items dynamically update (enable/disable, title changes)
- [ ] Context menus on all interactive elements
- [ ] App menu has About, Settings, Hide, Quit

### Windows
- [ ] Windows are freely resizable with sensible minimums
- [ ] Fullscreen and Split View work
- [ ] Multiple windows supported (if appropriate)
- [ ] Window position and size persist across launches
- [ ] Traffic light buttons visible and functional
- [ ] Document title and edited state shown (if document-based)

### Toolbars
- [ ] Toolbar present with common actions
- [ ] Toolbar is user-customizable
- [ ] Search field available in toolbar

### Sidebars
- [ ] Sidebar for navigation (if app has multiple sections)
- [ ] Sidebar is collapsible
- [ ] Source list style with vibrancy

### Keyboard
- [ ] Full keyboard navigation (Tab, arrows, Enter, Esc)
- [ ] Cmd+Z undo for all destructive actions
- [ ] Space for Quick Look previews
- [ ] Delete key removes selected items
- [ ] No keyboard traps (user can always Tab out)

### Pointer
- [ ] Hover states on interactive elements
- [ ] Right-click context menus everywhere
- [ ] Drag and drop for content manipulation
- [ ] Cmd+Click for multi-selection
- [ ] Appropriate cursor changes

### Notifications
- [ ] Notifications only for important events
- [ ] Alerts have suppression option for recurring ones
- [ ] No modal alerts for routine operations

### System Integration
- [ ] High-quality Dock icon
- [ ] Content indexed in Spotlight (if applicable)
- [ ] Share menu works
- [ ] App Intents for Shortcuts

### Visual Design
- [ ] System fonts at semantic sizes
- [ ] Dark Mode fully supported
- [ ] System accent color respected
- [ ] Translucency respects accessibility setting
- [ ] Consistent spacing on 8pt grid

---

## Anti-Patterns

**Do not do these things in a Mac app:**

1. **No menu bar** — Every Mac app needs a menu bar. Period. A Mac app without menus is like a car without a steering wheel.

2. **Hamburger menus** — Never use a hamburger menu on Mac. The menu bar exists for this purpose. Hamburger menus signal a lazy iOS port.

3. **Tab bars at the bottom** — Mac apps use sidebars and toolbars, not iOS-style tab bars. If you need tabs, use actual document tabs in the tab bar (like Safari or Finder).

4. **Large touch-sized targets** — Mac controls should be compact (22-28pt height). Users have precise pointer input. Giant buttons waste space and look out of place.

5. **Floating action buttons** — FABs are a Material Design pattern. On Mac, place primary actions in the toolbar, menu bar, or as inline buttons.

6. **Sheet for every action** — Don't use modal sheets for simple operations. Use popovers, inline editing, or direct manipulation. Sheets should be reserved for multi-step workflows or important decisions.

7. **Custom window chrome** — Don't replace the standard title bar, traffic lights, or window controls with custom implementations. Users expect these to work consistently across all apps.

8. **Ignoring keyboard** — If a power user must reach for the mouse to perform common actions, your keyboard support is insufficient.

9. **Single-window only** — Unless your app is genuinely single-purpose (calculator, timer), support multiple windows. Users expect to Cmd+N for new windows.

10. **Fixed window size** — Non-resizable windows feel broken on Mac. Users have displays ranging from 13" laptops to 32" externals and expect to use that space.

11. **No Cmd+Z undo** — Every destructive or modifying action must be undoable. Users build muscle memory around Cmd+Z as their safety net.

12. **Notification spam** — Mac apps that send excessive notifications get their permissions revoked. Only notify for events that genuinely need attention.

13. **Ignoring Dark Mode** — A Mac app that looks wrong in Dark Mode appears abandoned. Always test both appearances.

14. **Hardcoded colors** — Use semantic system colors, not hardcoded hex values. Your colors should adapt to Light/Dark mode and accessibility settings automatically.

15. **No drag and drop** — Mac is a drag-and-drop platform. If users can see content, they expect to drag it somewhere.
