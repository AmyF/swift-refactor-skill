# Swift Code Analysis Methods

Use this guide to trace SwiftUI entries, call relationships, async boundaries, data flow, and modern system experience boundaries for iOS 18 / Swift 6.2-ready projects.

## Analysis Flow

```
Entry identification -> Call path tracing -> State/data flow -> Report
```

## 1. Entry Identification

Find user-facing triggers and app lifecycle entries.

```bash
rg '@main|struct .*: App|var body: some Scene' --glob '*.swift'
rg 'Button\(|Menu\(|Toggle\(|Picker\(|TextField\(|onSubmit\(' --glob '*.swift'
rg '\.task\(|\.onAppear\(|\.onDisappear\(|\.onChange\(' --glob '*.swift'
rg 'NavigationStack|NavigationSplitView|navigationDestination|sheet\(|fullScreenCover\(|scrollPosition|contentTransition' --glob '*.swift'
rg 'AppIntent|AppEntity|EntityQuery|IndexedEntity|Transferable|SnippetIntent|AppShortcutsProvider' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
```

Record entries as:

| File | Entry | Trigger | Frequency |
|------|-------|---------|-----------|
| `FeatureView.swift:42` | `Button` action | User tap | High |
| `FeatureView.swift:66` | `.task` | View load | Medium |

## 2. Call Path Tracing

Trace from SwiftUI event to model, service, actor, and persistence/network boundaries.

```bash
rg 'func load|func save|func submit|func refresh' --glob '*.swift'
rg 'await |Task \{|async throws|async ->' --glob '*.swift'
rg 'actor |@MainActor|nonisolated|@concurrent|Sendable|@preconcurrency' --glob '*.swift'
rg 'protocol .*Service|struct .*Service|final class .*Service' --glob '*.swift'
```

Use forward tracing for feature behavior and backward tracing for risky shared functions.

```
Button action
    ↓
FeatureModel.submit()
    ↓ await
FeatureService.save()
    ↓
PersistenceActor.write()
    ↓
SwiftData / file / network boundary
```

Capture:

- UI trigger.
- State owner.
- Service or actor call.
- Async and actor isolation boundary.
- Default actor isolation and intentional concurrent execution.
- Error handling site.
- Rendering output affected by the result.
- SwiftData or App Intents boundary when present.

## 3. State and Data Flow

Trace model creation, mutation, binding, transformation, and persistence.

```bash
rg '@Observable|@Bindable|@State|@Environment|EnvironmentKey' --glob '*.swift'
rg 'Binding<|@Binding|withAnimation|transaction' --glob '*.swift'
rg 'Codable|Decodable|Encodable|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
rg 'AppEntity|IndexedEntity|Transferable|IntentFile|LocalizedStringResource' --glob '*.swift'
rg 'Result<|throws|do \{|catch ' --glob '*.swift'
```

Document the flow:

```
User input
    ↓ Binding
FeatureDraft
    ↓ validation
Domain model
    ↓ async service
Persistence/network request
    ↓ result
Observable model mutation
    ↓ SwiftUI render
```

## 4. Output Template

```markdown
# Code Analysis Report: {Feature}

## Entries
| File | Entry | Trigger | Frequency |
|------|-------|---------|-----------|

## Call Paths
1. {entry} -> {model/service/actor chain}

## State and Data Flow
- State owner:
- Bindings:
- Async boundaries:
- Actor isolation:
- Modern Swift posture:
- SwiftData/App Intents boundary:
- Error handling:

## Risks
- [ ] Swift 6 concurrency issue:
- [ ] Swift 6.2 readiness issue:
- [ ] Legacy state pattern:
- [ ] SwiftData lifetime issue:
- [ ] App Intents entity/query issue:
- [ ] Duplicate service or View:
- [ ] UI behavior preservation risk:
```
