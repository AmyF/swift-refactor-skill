# SwiftUI Vibe-Coding Problem Classification

AI-generated SwiftUI apps often drift because each session sees only part of the app. The result is local reinvention instead of reuse.

## Problem Overview

```
SwiftUI Vibe-Coding Problems
├── Resource waste
│   ├── DesignSystem not reused
│   ├── ViewModifiers and Styles duplicated
│   ├── Services and clients reimplemented
│   └── Preview fixtures duplicated
├── Legacy state management
│   ├── ObservableObject
│   ├── @Published
│   ├── @StateObject
│   └── @ObservedObject
├── Pattern chaos
│   ├── Multiple loading/error styles
│   ├── Navigation state split across Views
│   ├── Inconsistent service protocols
│   └── Mixed actor isolation
├── Swift safety issues
│   ├── Force unwraps and forced casts
│   ├── Unstructured tasks
│   ├── Broad @MainActor usage
│   ├── Unchecked sendability
│   └── Unexplained @preconcurrency / nonisolated
├── Modern Swift gaps
│   ├── Swift 6.2 settings not audited
│   ├── SwiftData lifetime confusion
│   ├── App Intents entities incomplete
│   ├── XCTest-only new coverage
│   └── Older SwiftUI wrappers replacing modern APIs
└── Architecture decay
    ├── Feature-to-feature coupling
    ├── UI leaking into Domain or Infrastructure
    ├── Scattered conditional compilation
    └── Unclear target boundaries
```

## 1. DesignSystem Not Reused

**Symptom**: features implement local cards, rows, buttons, empty states, loading states, colors, and spacing that already exist.

```bash
rg 'struct .*: View' --glob '*.swift'
rg 'ButtonStyle|LabelStyle|ToggleStyle|ViewModifier' --glob '*.swift'
rg 'Color\(|Font\.|\.padding\(|\.cornerRadius\(|\.shadow\(' --glob '*.swift'
```

Fix:

1. Inventory DesignSystem components and tokens.
2. Compare local implementations with reusable resources.
3. Replace equivalent local code.
4. Extract genuinely reusable variants into DesignSystem.

## 2. Legacy State Management

Legacy patterns to migrate away from:

```bash
rg 'ObservableObject|@Published|@StateObject|@ObservedObject' --glob '*.swift'
```

Preferred targets:

- `@Observable` for state models.
- `@Bindable` for child editing.
- `@State` for view-owned model lifetime.
- `@Environment` for injected dependencies.
- Actors for mutable shared non-UI state.

Fix:

1. Identify the owner and lifetime of each legacy model.
2. Replace publisher-driven state with observable stored properties.
3. Move bindings to `@Bindable` where editing is needed.
4. Keep UI mutations on the main actor.
5. Re-run Swift 6 build/test verification.

## 3. Duplicate Services and Clients

**Symptom**: multiple features own similar networking, persistence, logging, settings, or permission logic.

```bash
rg 'URLSession|URLRequest|FileManager|UserDefaults|ModelContext' --glob '*.swift'
rg 'protocol .*Service|struct .*Service|final class .*Service|actor .*Service' --glob '*.swift'
```

Fix:

1. Choose or create one service protocol.
2. Move implementation to Services or Infrastructure.
3. Inject the service through initializer or environment.
4. Replace direct calls in features.

## 4. State Source Duplication

**Symptom**: the same selection, navigation path, presentation flag, loading state, or error appears in multiple Views.

```bash
rg '@State .*is|@State .*selected|@State .*path|@State .*error' --glob '*.swift'
rg '\.sheet\(|\.alert\(|NavigationPath|navigationDestination' --glob '*.swift'
```

Fix:

1. Choose a single state owner.
2. Pass bindings only to Views that mutate.
3. Convert derived values to computed properties.
4. Remove sync code between duplicate sources.

## 5. Swift 6.2+ Concurrency Risks

**Symptoms**: unstructured tasks, unsafe shared mutable state, excessive main actor isolation, or unchecked sendability.

```bash
rg 'Task \{|Task\.detached|DispatchQueue' --glob '*.swift'
rg 'actor |@MainActor|nonisolated|@concurrent|Sendable|@preconcurrency|@unchecked Sendable|nonisolated\(unsafe\)' --glob '*.swift'
rg 'defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
```

Fix:

1. Prefer structured async APIs.
2. Use actors for mutable shared non-UI state.
3. Keep UI state isolated to main actor without blindly isolating libraries.
4. Make cross-task values safely `Sendable`.
5. Document any `@preconcurrency`, unchecked sendability, or unsafe non-isolation.

## 6. SwiftData Lifetime Confusion

**Symptoms**: `ModelContext` or `@Query` is passed through layers without ownership, or SwiftData models double as service DTOs.

```bash
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
```

Fix:

1. Define where model containers and contexts are created.
2. Keep persistence concerns out of reusable Views and pure Domain.
3. Convert persistence models to domain/view models at boundaries when needed.
4. Test persistence flows with isolated fixtures.

## 7. App Intents and System Experience Gaps

**Symptoms**: user-visible app entities or actions exist but are not exposed consistently to Shortcuts, Spotlight, controls, widgets, or interactive snippets.

```bash
rg 'AppIntent|AppEntity|EntityQuery|IndexedEntity|Transferable|SnippetIntent|ControlConfigurationIntent' --glob '*.swift'
```

Fix:

1. Identify stable user-facing entities and actions.
2. Add or clean up App Entity and query boundaries.
3. Use `IndexedEntity` and `Transferable` only for content that benefits from system exposure.
4. Keep intent execution routed through the same services as the app UI.

## 8. Older SwiftUI Wrappers Instead of Modern APIs

**Symptoms**: custom type erasure, wrapper Views, or imperative scrolling/navigation exist where SwiftUI has native APIs.

```bash
rg 'AnyView|UIViewRepresentable|UIViewControllerRepresentable|NavigationLink|NavigationPath' --glob '*.swift'
rg 'Layout|contentTransition|scrollPosition|#Preview' --glob '*.swift'
```

Fix:

1. Prefer typed navigation and explicit presentation state.
2. Use `#Preview` and centralized preview fixtures.
3. Prefer custom `Layout`, `contentTransition`, and `scrollPosition` before bespoke wrappers.
4. Keep UIKit interop only when SwiftUI does not provide a clean solution.

## 9. Overgrown SwiftUI Views

**Symptoms**: large `body`, many private computed Views, too much local state, side effects inside View construction.

```bash
rg 'var body: some View|private var .*: some View|@ViewBuilder' --glob '*.swift'
rg '@State|@Binding|@Bindable|@Environment' --glob '*.swift'
```

Fix:

1. Extract stable subviews with explicit inputs.
2. Move business logic to observable models or services.
3. Keep View construction declarative.
4. Preserve visual output and interaction behavior.

## Priority Matrix

| Problem | Priority | Reason |
|---------|----------|--------|
| Broken target dependency or compile failure | P0 | Blocks verification |
| Legacy state migration blocking Swift 6 safety | P1 | Core architecture risk |
| DesignSystem not reused | P1 | High cleanup return |
| Duplicate services | P1 | Maintenance and testing risk |
| Concurrency isolation issue | P1 | Runtime correctness risk |
| SwiftData lifetime confusion | P1 | Data correctness risk |
| App Intents entity/query gap | P2 | System experience risk |
| Overgrown Views | P2 | Readability and performance risk |
| Older SwiftUI wrappers | P2 | Modernization and maintainability risk |
| Hardcoded tokens or strings | P2 | Product consistency risk |
| Minor naming drift | P3 | Cleanup only |
