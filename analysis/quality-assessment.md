# Swift Quality Assessment Methods

Assess maintainability, Swift 6.2+ readiness, SwiftUI complexity, and technical debt.

## 1. Complexity Metrics

| Metric | Threshold | Meaning |
|--------|-----------|---------|
| File length | >500 lines | Consider splitting by feature or component |
| View `body` length | >80 lines | Extract dedicated subviews |
| Function length | >50 lines | Split or move to model/service |
| Nesting depth | >3 levels | Simplify control flow |
| Stored state count in a View | >5 | Reconsider state ownership |

Detection:

```bash
rg --files -g '*.swift' | xargs wc -l | sort -rn | head -20
rg 'var body: some View' -n --glob '*.swift'
rg '^    func |^    private func |^    static func ' --glob '*.swift'
rg '@State|@Binding|@Bindable|@Environment' --glob '*.swift'
```

## 2. Swift Safety Smells

```bash
rg '!' --glob '*.swift'
rg 'try!|as!|fatalError\(|preconditionFailure\(' --glob '*.swift'
rg 'AnyView' --glob '*.swift'
rg 'Task\.detached|DispatchQueue\.main|DispatchQueue\.global' --glob '*.swift'
rg '@concurrent|@preconcurrency|@unchecked Sendable|nonisolated\(unsafe\)' --glob '*.swift'
rg 'ObservableObject|@Published|@StateObject|@ObservedObject' --glob '*.swift'
```

Classify each hit. Not every `!` is wrong, but force unwraps, forced casts, unchecked sendability, and broad main-thread dispatch require review.

## 3. Swift 6.2+ Concurrency Assessment

Check:

- `Sendable` correctness for values crossing tasks or actors.
- Actor isolation for mutable shared state.
- `@MainActor` only around UI state and main-thread APIs.
- Default actor isolation is intentional per target.
- `@concurrent` marks deliberate concurrent execution, not diagnostic avoidance.
- `nonisolated`, `@preconcurrency`, and unchecked sendability have documented reasons.
- No unstructured tasks that outlive the owning View/model without cancellation.
- Async service APIs surface cancellation and errors cleanly.

```bash
rg 'defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
rg 'async |await |throws|Task \{|TaskGroup|withTaskGroup|withThrowingTaskGroup' --glob '*.swift'
rg 'actor |@MainActor|nonisolated|@concurrent|Sendable|@preconcurrency' --glob '*.swift'
```

## 4. SwiftUI Quality Assessment

Check:

- Reusable Views instead of large inline private computed Views.
- Stable state ownership and a single source of truth.
- Bindings only where mutation is required.
- DesignSystem tokens instead of hardcoded styling.
- Preview data centralized and maintained.
- Navigation and presentation state is explicit and testable.
- Typed navigation, custom `Layout`, `contentTransition`, and `scrollPosition` are considered before custom wrappers or type erasure.

```bash
rg 'private var .*: some View|@ViewBuilder' --glob '*.swift'
rg '\.sheet\(|\.alert\(|\.confirmationDialog\(|\.navigationDestination|scrollPosition|contentTransition' --glob '*.swift'
rg 'Color\(|Font\.|\.cornerRadius\(|\.shadow\(|\.padding\(' --glob '*.swift'
rg '#Preview|PreviewProvider|Layout' --glob '*.swift'
```

## 5. Data, Intents, Tests, and Preview Coverage

```bash
rg --files -g '*Tests.swift' -g '*UITests.swift'
rg 'XCTest|Testing|@Test|func test' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
rg 'AppIntent|AppEntity|IndexedEntity|Transferable|SnippetIntent|AppShortcutsProvider' --glob '*.swift'
rg '#Preview|PreviewProvider' --glob '*.swift'
```

Assess:

- Swift Testing for new domain and service tests.
- Existing XCTest coverage that should be preserved.
- UI tests for critical flows.
- Previews for important SwiftUI states.
- Mock services or fixtures for async states and errors.
- SwiftData model/context ownership and test isolation.
- App Intents entity/query coverage for system experiences.

## 6. Quality Report Template

```markdown
# Quality Assessment Report

## Complexity
| File/View | Metric | Value | Recommendation |
|-----------|--------|-------|----------------|

## Swift Safety
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

## Concurrency
| Component | Risk | Recommendation |
|-----------|------|----------------|

## Modern Swift Readiness
| Area | Current | Gap |
|------|---------|-----|
| Swift 6.2 settings | | |
| Swift Testing | | |
| SwiftData | | |
| App Intents/System Experiences | | |

## SwiftUI
| Issue | Location | Recommendation |
|-------|----------|----------------|

## Tests and Previews
| Area | Coverage | Gap |
|------|----------|-----|

## Top Recommendations
1. ...
```
