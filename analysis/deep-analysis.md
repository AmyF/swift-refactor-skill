# SwiftUI Deep Analysis Methods

Deep analysis finds the problems that AI-generated SwiftUI code tends to hide: duplicate Views, unused design-system pieces, inconsistent services, legacy state ownership, unsafe concurrency boundaries, SwiftData lifetime issues, and incomplete App Intents system experiences.

## Core Principle

Analyze each key feature one by one. Compare what the feature implemented locally against the reusable resources discovered in Phase 0.

## 1. Duplicate Implementation Detection

### Duplicate Views and Modifiers

```bash
rg 'struct .*: View' --glob '*.swift'
rg 'struct .*Modifier: ViewModifier|func body\(content:' --glob '*.swift'
rg 'ButtonStyle|LabelStyle|ToggleStyle|TextFieldStyle' --glob '*.swift'
rg 'private var .*: some View|@ViewBuilder' --glob '*.swift'
```

Look for local cards, rows, buttons, empty states, loading states, error banners, and formatting Views that should use DesignSystem components.

### Duplicate Services and Clients

```bash
rg 'protocol .*Service|struct .*Service|final class .*Service|actor .*Service' --glob '*.swift'
rg 'URLSession|URLRequest|ModelContext|FileManager|UserDefaults' --glob '*.swift'
```

Check whether multiple features implement the same networking, persistence, configuration, logging, or permission behavior.

### Duplicate Models and Formatting

```bash
rg 'struct .*: (Identifiable|Codable)|enum .*: String' --glob '*.swift'
rg 'DateFormatter|FormatStyle|formatted\(|NumberFormatter' --glob '*.swift'
rg 'extension String|extension Date|extension View' --glob '*.swift'
```

Unify shared domain concepts and formatting helpers when they represent the same behavior.

## 2. Resource Reuse Analysis

Compare each feature against the Phase 0 inventory.

| Resource | Check |
|----------|-------|
| DesignSystem Views | Is the feature creating local equivalents? |
| ViewModifiers and Styles | Are colors, typography, spacing, and controls hardcoded? |
| Assets | Are image, color, and symbol names duplicated or stringly typed? |
| Localizable strings | Are user-facing strings hardcoded? |
| Environment values | Are dependencies passed consistently? |
| Services and protocols | Does the feature bypass shared service abstractions? |
| SwiftData | Are `@Model`, `@Query`, `ModelContainer`, and `ModelContext` lifetimes explicit? |
| App Intents | Are app entities, queries, Spotlight exposure, and transfer models consistent? |
| Preview fixtures | Does the feature duplicate mocks or sample data? |

Useful searches:

```bash
rg 'Color\(|Font\.|\.font\(|\.padding\(|\.cornerRadius\(' --glob '*.swift'
rg 'Image\("|Label\("|Text\("' --glob '*.swift'
rg '@Environment\(|EnvironmentKey|\.environment\(' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
rg 'AppIntent|AppEntity|IndexedEntity|Transferable|SnippetIntent' --glob '*.swift'
rg '#Preview|PreviewProvider' --glob '*.swift'
```

## 3. Observation Macro Migration

Legacy patterns to migrate away from:

```bash
rg 'ObservableObject|@Published|@StateObject|@ObservedObject' --glob '*.swift'
```

Preferred target patterns:

- `@Observable` for reference models that SwiftUI observes.
- `@Bindable` when a child View edits observable model state.
- `@State` for view-owned values and model lifetime.
- `@Environment` for app-level dependencies or shared observable objects.
- Actor-isolated services for mutable shared non-UI state.
- `@MainActor` only where UI state or main-thread APIs require it.

Record every legacy state owner with:

- Current owner and lifetime.
- Read/write sites.
- Binding requirements.
- Async mutation points.
- Migration target.

## 4. Frontier Modern Swift Checks

```bash
rg 'defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
rg '@MainActor|nonisolated|@concurrent|@preconcurrency|@unchecked Sendable|nonisolated\(unsafe\)' --glob '*.swift'
rg 'Testing|@Test|XCTestCase|func test' --glob '*.swift'
rg 'AnyView|DispatchQueue|Task\.detached' --glob '*.swift'
rg 'Layout|contentTransition|scrollPosition|#Preview' --glob '*.swift'
```

Check whether:

- UI executable targets and library targets have intentional isolation defaults.
- `@concurrent` and `nonisolated` are used to express concurrency rather than silence diagnostics.
- Swift Testing is preferred for new unit tests.
- SwiftData ownership is separated from view rendering and service boundaries.
- App Intents expose stable app entities, `IndexedEntity`, `Transferable`, and interactive snippets only when useful.
- Modern SwiftUI APIs replace wrapper-heavy or type-erased patterns.

## 5. Pattern Consistency

Check:

- Do services use protocols consistently for testability?
- Are async calls made through structured concurrency instead of detached untracked work?
- Are errors surfaced consistently to the UI?
- Are loading and empty states consistent?
- Are navigation and presentation controlled by one state source?
- Are actor and `@MainActor` boundaries explicit?

## 6. Module Report Template

```markdown
# Feature Analysis: {Feature}

## Basic Information
- Entry:
- Files/targets:
- User flow:
- Current tests/previews:

## Call Path
{entry -> model -> service -> actor/persistence/network -> render}

## Resource Reuse
| Local Implementation | Existing Resource | Recommendation |
|----------------------|-------------------|----------------|

## Observation and State
| State Owner | Pattern | Risk | Target |
|-------------|---------|------|--------|

## Swift 6 Concurrency
| Location | Risk | Recommendation |
|----------|------|----------------|

## Modern Swift Readiness
| Area | Current | Recommendation |
|------|---------|----------------|
| Swift 6.2 settings | | |
| Swift Testing | | |
| SwiftData | | |
| App Intents/System Experiences | | |

## Problems
| Problem | Location | Priority | Fix |
|---------|----------|----------|-----|

## Refactoring Tasks
1. [M-001] ...
```
