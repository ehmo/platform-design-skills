# iPadOS Design Guidelines - Sections

## Section 1: Responsive Layout
- **Impact**: CRITICAL
- **Scope**: Adaptive layouts, size classes, column-based design, safe areas, all iPad screen sizes
- **Applies when**: Building any iPad UI, handling regular/compact width transitions, supporting iPad Mini through 13" Pro

## Section 2: Multitasking
- **Impact**: CRITICAL
- **Scope**: Split View, Slide Over, Stage Manager, resizable windows, multiple scenes
- **Applies when**: App runs alongside other apps, user resizes windows, app enters compact width via multitasking

## Section 3: Navigation
- **Impact**: HIGH
- **Scope**: Sidebar navigation, tab-to-sidebar conversion, three-column layout, toolbar placement, detail views
- **Applies when**: Designing primary navigation, building information hierarchies, placing toolbars and actions

## Section 4: Pointer & Trackpad
- **Impact**: HIGH
- **Scope**: Hover effects, pointer magnetism, right-click context menus, scroll behaviors, cursor customization, pointer-driven drag and drop
- **Applies when**: Any interactive element needs pointer adaptation, supporting Magic Keyboard or trackpad input

## Section 5: Keyboard
- **Impact**: HIGH
- **Scope**: Cmd+key shortcuts, discoverability overlay, tab navigation, hardware keyboard detection, system shortcut conflicts
- **Applies when**: Adding keyboard shortcuts, building forms, supporting hardware keyboard users

## Section 6: Apple Pencil
- **Impact**: MEDIUM
- **Scope**: Scribble handwriting input, double-tap tool switching, pressure/tilt sensitivity, hover detection, PencilKit
- **Applies when**: Building drawing or note-taking features, supporting handwriting input in text fields

## Section 7: Drag and Drop
- **Impact**: HIGH
- **Scope**: Inter-app drag and drop, multi-item selection, spring-loaded interactions, Universal Control, drop delegates
- **Applies when**: Content can be moved between apps, supporting file/image/text transfers, building content organization features

## Section 8: External Display
- **Impact**: MEDIUM
- **Scope**: Extended display content, AirPlay support, display connection/disconnection handling
- **Applies when**: App supports presentation mode, external monitors, or AirPlay output
