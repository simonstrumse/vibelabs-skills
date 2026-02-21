---
name: ios-development
description: Comprehensive iOS app development skill with Liquid Glass design, modern Swift patterns (@Observable, SwiftData), and App Store readiness. Self-updates by checking Apple Developer News for new iOS versions. Use for any iOS/SwiftUI project.
---

# iOS Development Skill

Complete iOS development toolkit: design system, architecture patterns, and App Store verification. **Self-updating** - checks Apple Developer News for iOS changes.

**Team ID:** `YOUR_TEAM_ID`
**Current Target:** iOS 26 (Liquid Glass)
**Minimum:** iOS 17.0 (for @Observable, SwiftData)
**Last Updated:** December 2025

---

## Self-Update Protocol

**When starting a new iOS project or major feature, ALWAYS run this check:**

```
BEFORE implementing iOS features, search the web for:
1. "Apple Developer News iOS [current year]"
2. "iOS [latest version] SwiftUI new APIs"
3. "WWDC [current year] SwiftUI changes"

Check for:
- New iOS version announcements
- Deprecated APIs
- New required APIs (privacy, permissions)
- Design system changes
- App Store requirement changes

If significant changes found, update this skill file before proceeding.
```

**Why self-update?** Apple deprecates APIs and mandates new patterns yearly. iOS 26 made Liquid Glass mandatory. iOS 27 will remove the option to retain old designs.

---

## Quick Reference

### Modern Swift Stack (iOS 17+)

| Layer | Technology | NOT This |
|-------|------------|----------|
| State | `@Observable` | ~~ObservableObject~~ |
| Bindings | `@State`, `@Bindable` | ~~@StateObject, @ObservedObject~~ |
| Persistence | SwiftData + VersionedSchema | ~~Core Data, UserDefaults~~ |
| UI | SwiftUI + Liquid Glass | ~~UIKit wrappers~~ |
| Architecture | MVVM with @Query | ~~MVC~~ |

### Glass Effect Quick Reference

```swift
// Basic glass
.glassEffect()

// Tinted + interactive
.glassEffect(.regular.tint(.blue).interactive())

// Group related elements
GlassEffectContainer { ... }

// Morphing sheet
.matchedTransitionSource(id: "x", in: namespace)
.navigationTransition(.zoom(sourceID: "x", in: namespace))
```

---

# PART 1: LIQUID GLASS DESIGN SYSTEM

## Design Philosophy

Liquid Glass is Apple's translucent material that reflects and refracts surroundings while dynamically transforming to bring focus to content.

### Core Principles

1. **Dynamic Transparency** - Interfaces feel like overlapping sheets of glass
2. **Glass is Overlay** - Apply ONLY to elements on TOP of UI (toolbars, FABs, modals)
3. **Fluid Interaction** - Morphing transitions connect related elements
4. **Depth and Context** - Elements create sense of spatial hierarchy
5. **Colorful Backgrounds** - Vibrant content behind glass makes effects shine

### Layout Rules

- **Keep essential UI in central 70% of canvas**
- Let tab bars auto-collapse on scroll
- Think in layers: content at bottom, glass controls on top
- Apply glass sparingly (key surfaces only)
- Use native Apple components (optimized for Liquid Glass)

### What NOT to Do

- Don't create custom components (poor Liquid Glass compatibility)
- Don't align thin borders exactly with mask edges
- Don't overuse glass effects
- Don't apply glass to main content areas
- Don't stack multiple glass layers

**MANDATORY by iOS 27** - Apple will remove option to retain old designs.

---

## Glass Effect APIs

### Basic Glass Effect

```swift
// Simple glass background
Button("Action") { }
    .glassEffect()

// Tinted glass with color
Button("Save") { }
    .glassEffect(.regular.tint(.blue))

// Interactive glass (grows + shimmers on touch)
Button("Tap Me") { }
    .glassEffect(.regular.tint(.purple.opacity(0.8)).interactive())

// Button style alternative
Button("Glass Button") { }
    .buttonStyle(.glass)
```

### Glass Effect Container

Group elements to blend together:

```swift
GlassEffectContainer {
    HStack(spacing: 12) {
        Button(action: {}) {
            Image(systemName: "camera")
        }
        .glassEffect()

        Button(action: {}) {
            Image(systemName: "photo")
        }
        .glassEffect()
    }
}
```

### Glass Effect Identity

Link related elements across view hierarchy:

```swift
@Namespace private var glassNamespace

VStack {
    Button("Expand") { }
        .glassEffectID("action", in: glassNamespace)

    if expanded {
        DetailPanel()
            .glassEffectID("action", in: glassNamespace)
    }
}
```

### Custom Glass Shapes

```swift
// Circular glass
Circle()
    .glassEffect()
    .frame(width: 60, height: 60)

// Custom shape
CustomPath()
    .glassEffect(.regular, in: CustomShape())
```

---

## Liquid Glass Sheets

### Partial Height (Floating)

```swift
.sheet(isPresented: $showSheet) {
    ContentView()
        .presentationDetents([.medium, .large])
        // Auto-gets Liquid Glass background
}
```

### Morphing Transitions

```swift
struct ContentView: View {
    @Namespace private var transition
    @State private var showInfo = false

    var body: some View {
        NavigationStack {
            MainContent()
                .toolbar {
                    ToolbarItem(placement: .bottomBar) {
                        Button("Info", systemImage: "info") {
                            showInfo = true
                        }
                    }
                    .matchedTransitionSource(id: "info", in: transition)
                }
                .sheet(isPresented: $showInfo) {
                    InfoView()
                        .presentationDetents([.medium, .large])
                        .navigationTransition(.zoom(sourceID: "info", in: transition))
                }
        }
    }
}
```

---

## Component Patterns

### Floating Action Bar

```swift
struct FloatingActionBar: View {
    let actions: [ActionItem]

    var body: some View {
        GlassEffectContainer {
            HStack(spacing: 16) {
                ForEach(actions) { action in
                    Button(action: action.handler) {
                        Image(systemName: action.icon)
                            .font(.title2)
                    }
                    .glassEffect(.regular.interactive())
                }
            }
            .padding(.horizontal, 20)
            .padding(.vertical, 12)
        }
        .glassEffect()
    }
}
```

### Glass Card

```swift
struct GlassCard<Content: View>: View {
    @ViewBuilder let content: Content

    var body: some View {
        content
            .padding(20)
            .background {
                RoundedRectangle(cornerRadius: 24)
                    .glassEffect(.regular.tint(.white.opacity(0.1)))
            }
    }
}
```

### Animated Counter

```swift
struct GlassCounter: View {
    let value: Int

    var body: some View {
        Text("\(value)")
            .font(.system(size: 48, weight: .bold, design: .rounded))
            .contentTransition(.numericText())
            .padding(24)
            .glassEffect(.regular.tint(.blue.opacity(0.3)).interactive())
            .animation(.spring(duration: 0.3), value: value)
    }
}
```

---

# PART 2: MODERN SWIFT ARCHITECTURE

## State Management: @Observable

**CRITICAL:** Always use @Observable (iOS 17+). ObservableObject is legacy.

```swift
import Observation

@Observable
final class MyViewModel {
    var title: String = ""
    var items: [Item] = []
    var isLoading: Bool = false

    // NO @Published needed!
    // Views auto-update when properties they READ change

    func loadItems() async {
        isLoading = true
        items = await fetchItems()
        isLoading = false
    }
}
```

**In Views:**

```swift
struct MyView: View {
    @State private var viewModel = MyViewModel()  // NOT @StateObject

    var body: some View {
        List(viewModel.items) { item in
            Text(item.name)
        }
        .task {
            await viewModel.loadItems()
        }
    }
}
```

**Why @Observable?**
- **Performance**: Views only re-render when properties they actually read change
- **Simplicity**: No @Published, @ObservedObject, @StateObject boilerplate
- **Apple recommended**: "For new development, using Observable is the easiest way"

---

## SwiftData: ALWAYS Use VersionedSchema

**Never ship unversioned SwiftData.** Start with VersionedSchema from day one to avoid migration crashes.

```swift
import SwiftData

// ALWAYS start versioned
enum AppSchemaV1: VersionedSchema {
    static var versionIdentifier = Schema.Version(1, 0, 0)

    static var models: [any PersistentModel.Type] {
        [Item.self, Category.self]
    }

    @Model
    final class Item {
        var id: UUID
        var name: String
        var createdAt: Date

        init(name: String) {
            self.id = UUID()
            self.name = name
            self.createdAt = Date()
        }
    }

    @Model
    final class Category {
        var id: UUID
        var name: String

        @Relationship(deleteRule: .cascade)
        var items: [Item] = []

        init(name: String) {
            self.id = UUID()
            self.name = name
        }
    }
}

// Migration Plan (even for V1)
enum AppMigrationPlan: SchemaMigrationPlan {
    static var schemas: [any VersionedSchema.Type] {
        [AppSchemaV1.self]
    }

    static var stages: [MigrationStage] { [] }
}
```

### Adding V2 Later

```swift
enum AppSchemaV2: VersionedSchema {
    static var versionIdentifier = Schema.Version(2, 0, 0)

    @Model
    final class Item {
        var id: UUID

        // RENAMING: Use @Attribute(originalName:)
        @Attribute(originalName: "name")
        var title: String

        var createdAt: Date

        // NEW PROPERTY: Provide default
        var priority: Int = 0
    }
}

enum AppMigrationPlan: SchemaMigrationPlan {
    static var schemas: [any VersionedSchema.Type] {
        [AppSchemaV1.self, AppSchemaV2.self]
    }

    static var stages: [MigrationStage] {
        [migrateV1toV2]
    }

    static let migrateV1toV2 = MigrationStage.lightweight(
        fromVersion: AppSchemaV1.self,
        toVersion: AppSchemaV2.self
    )
}
```

### Migration Rules

**Lightweight (automatic):**
- Adding new models
- Adding properties with default values
- Deleting properties
- Renaming with `@Attribute(originalName:)`

**Crashes if you:**
- Ship unversioned, then add VersionedSchema
- Add `.unique` when duplicates exist
- Rename without `@Attribute(originalName:)`

---

## MVVM Pattern with SwiftData

```swift
// VIEW: @Query for reactive data
struct ItemListView: View {
    @Query(sort: \Item.createdAt, order: .reverse)
    private var items: [Item]

    @State private var viewModel = ItemListViewModel()
    @Environment(\.modelContext) private var modelContext

    var body: some View {
        List(items) { item in
            ItemRow(item: item)
        }
        .toolbar {
            Button("Add") {
                viewModel.addItem(context: modelContext)
            }
            .glassEffect(.regular.interactive())
        }
    }
}

// VIEWMODEL: Operations only
@Observable
final class ItemListViewModel {
    var newItemName = ""

    func addItem(context: ModelContext) {
        let item = Item(name: newItemName)
        context.insert(item)
        try? context.save()
        newItemName = ""
    }

    func deleteItem(_ item: Item, context: ModelContext) {
        context.delete(item)
        try? context.save()
    }
}
```

**Pattern:**
- @Query in Views for reactive display
- ViewModels for business logic
- Pass ModelContext to methods (don't store as property)

---

## App Entry Point

```swift
import SwiftUI
import SwiftData

@main
struct MyApp: App {
    var sharedModelContainer: ModelContainer = {
        let schema = Schema(versionedSchema: AppSchemaV1.self)
        let config = ModelConfiguration(schema: schema, isStoredInMemoryOnly: false)

        do {
            return try ModelContainer(
                for: schema,
                migrationPlan: AppMigrationPlan.self,
                configurations: [config]
            )
        } catch {
            fatalError("Could not create ModelContainer: \(error)")
        }
    }()

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(sharedModelContainer)
    }
}
```

---

# PART 3: PROJECT STRUCTURE

```
ProjectName/
├── App/
│   ├── ProjectNameApp.swift           # @main, ModelContainer
│   └── AppConfig.swift                # Configuration constants
│
├── Models/
│   ├── Schemas/
│   │   ├── AppSchemaV1.swift          # VersionedSchema V1
│   │   └── AppMigrationPlan.swift     # SchemaMigrationPlan
│   └── Domain/
│       └── DomainModels.swift         # Non-persisted models
│
├── Views/
│   ├── ContentView.swift
│   ├── [Feature]/
│   │   ├── [Feature]View.swift
│   │   └── [Feature]Components.swift
│   └── Components/
│       ├── GlassCard.swift
│       └── FloatingActionBar.swift
│
├── ViewModels/
│   └── [Feature]ViewModel.swift       # @Observable ViewModels
│
├── Services/
│   ├── NetworkService.swift
│   └── [Domain]Service.swift
│
├── Utilities/
│   ├── Extensions/
│   └── Constants.swift
│
└── Resources/
    ├── Assets.xcassets
    ├── Localizable.xcstrings
    └── PrivacyInfo.xcprivacy          # Required since May 2024
```

---

# PART 4: APP STORE VERIFICATION CHECKLIST

Run before EVERY App Store submission.

## TIER 1: Critical (Prevents Rejections)

### Build Quality
- [ ] Clean Release build succeeds
- [ ] ZERO warnings in Release configuration
- [ ] BUILD_ACTIVE_ARCHITECTURE_ONLY = NO for Release

### Code Signing
- [ ] Team ID: `YOUR_TEAM_ID`
- [ ] Bundle identifier: reverse-DNS format
- [ ] Provisioning profile valid and not expired
- [ ] Deployment target iOS 17.0+

### Privacy & Security
- [ ] `PrivacyInfo.xcprivacy` exists (required since May 2024)
- [ ] Required reason APIs declared:
  - UserDefaults API
  - File timestamp APIs
  - System boot time APIs
  - Disk space APIs
- [ ] Privacy descriptions in Info.plist
- [ ] All URLs use HTTPS
- [ ] No hardcoded credentials or API keys

### Deprecated APIs
- [ ] No UIWebView (use WKWebView)
- [ ] No ObservableObject (use @Observable)
- [ ] No Core Data (use SwiftData)
- [ ] Third-party libraries iOS 26 compatible

### Assets
- [ ] App icon 1024x1024px present
- [ ] App icon has NO transparency
- [ ] App icon has NO applied effects
- [ ] Launch screen configured

## TIER 2: Quality

### Code Quality
- [ ] No placeholder text ("lorem ipsum", "TODO: Replace")
- [ ] No TODO/FIXME comments in production
- [ ] No debug print statements
- [ ] No forced unwraps in production paths

### Info.plist
- [ ] CFBundleVersion set
- [ ] CFBundleShortVersionString matches release
- [ ] CFBundleDisplayName set
- [ ] UISupportedInterfaceOrientations set

## TIER 3: iOS 26 Specific

- [ ] Built with Xcode 16+
- [ ] Liquid Glass design implemented
- [ ] Essential UI in central 70% of canvas
- [ ] No dependencies blocking iOS 26

---

# PART 5: DEPRECATED PATTERNS

| Pattern | Why Deprecated | Use Instead |
|---------|----------------|-------------|
| `ObservableObject` | Legacy, unnecessary re-renders | `@Observable` macro |
| `@Published` | Requires ObservableObject | Properties on @Observable |
| `@StateObject` | For ObservableObject | `@State` with @Observable |
| `@ObservedObject` | For ObservableObject | Direct access or `@Bindable` |
| Core Data | Legacy persistence | SwiftData |
| `UserDefaults` for models | Not type-safe | SwiftData |
| Unversioned SwiftData | Migration crashes | VersionedSchema from day one |
| `UIKit` wrappers | When SwiftUI native exists | Native SwiftUI |
| Custom tab bars | Poor Liquid Glass support | Native TabView |

---

# PART 6: UIKit INTEGRATION

For camera apps and other UIKit requirements:

```swift
// SwiftUI wrapper for UIKit view
struct CameraPreview: UIViewRepresentable {
    let session: AVCaptureSession

    func makeUIView(context: Context) -> PreviewView {
        let view = PreviewView()
        view.videoPreviewLayer.session = session
        view.videoPreviewLayer.videoGravity = .resizeAspectFill
        return view
    }

    func updateUIView(_ uiView: PreviewView, context: Context) {}
}

class PreviewView: UIView {
    override class var layerClass: AnyClass {
        AVCaptureVideoPreviewLayer.self
    }

    var videoPreviewLayer: AVCaptureVideoPreviewLayer {
        layer as! AVCaptureVideoPreviewLayer
    }
}
```

### UIKit Navigation Bar with SwiftUI Content

```swift
// In UIViewController
let hostingController = UIHostingController(rootView: SwiftUIToolbar())
hostingController.sizingOptions = [.intrinsicContentSize]
hostingController.view.setContentCompressionResistancePriority(.defaultHigh, for: .horizontal)
navigationItem.rightBarButtonItem = UIBarButtonItem(customView: hostingController.view)
```

---

# PART 7: PERFORMANCE

### Glass Effect Performance

- Glass uses `CABackdropLayer` - GPU intensive
- Test on target devices (iPhone XR, SE for baseline)
- Limit simultaneous glass surfaces to 3-5
- Avoid glass on rapidly-animating content
- Profile with Instruments if frame drops occur

### SwiftData Performance

```swift
// Batch operations
modelContext.autosaveEnabled = false
for item in items {
    modelContext.insert(item)
}
try modelContext.save()
modelContext.autosaveEnabled = true

// Efficient queries with predicates
@Query(filter: #Predicate<Item> { $0.isActive })
private var activeItems: [Item]
```

### @Observable Performance

Views only re-render when properties they **actually read** change:

```swift
@Observable
class ViewModel {
    var title: String = ""      // Change triggers re-render only if view reads title
    var internalState: Int = 0  // No re-render if view doesn't read this
}
```

---

# PART 8: COMMON COMMANDS

```bash
# Build and run
xcodebuild -scheme ProjectName -destination 'platform=iOS Simulator,name=iPhone 16 Pro'

# Run tests
xcodebuild test -scheme ProjectName -destination 'platform=iOS Simulator,name=iPhone 16 Pro'

# Clean build
xcodebuild clean -scheme ProjectName

# Archive for App Store
xcodebuild archive -scheme ProjectName -archivePath build/ProjectName.xcarchive

# Show build settings
xcodebuild -showBuildSettings | grep -E "TEAM|BUNDLE|TARGET"
```

---

## Quick Start Checklist

- [ ] Set deployment target iOS 17.0 minimum
- [ ] Configure Team ID: `YOUR_TEAM_ID`
- [ ] Create folder structure
- [ ] Define VersionedSchema from day one
- [ ] Use `@Observable` for all ViewModels
- [ ] Use `@State` for ViewModels in Views
- [ ] Use native Apple components (Liquid Glass optimized)
- [ ] Keep essential UI in central 70% of canvas
- [ ] Apply `.glassEffect()` only to overlay elements
- [ ] Create `PrivacyInfo.xcprivacy`
- [ ] Test on iOS 26 simulator
- [ ] Run verification checklist before submission

---

## App Store Text Rules

- **Never use emojis** in "What to Test" or App Store texts
- Follow Liquid Glass design for iOS 26+ screenshots
- Ensure app icon follows current guidelines
- Test on multiple device sizes

---

## Resources

- [Apple: Liquid Glass Overview](https://developer.apple.com/documentation/TechnologyOverviews/liquid-glass)
- [Apple: Applying Liquid Glass](https://developer.apple.com/documentation/SwiftUI/Applying-Liquid-Glass-to-custom-views)
- [WWDC25: Build with New Design](https://developer.apple.com/videos/play/wwdc2025/323/)
- [SwiftData Documentation](https://developer.apple.com/documentation/swiftdata)
- [Observable Macro](https://developer.apple.com/documentation/observation)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
