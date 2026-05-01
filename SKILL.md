---
name: swift-refactor
description: Frontier-modern SwiftUI-first iOS 18 and Swift 6.2+ refactoring methodology. Use for SwiftUI app architecture analysis, Xcode or SwiftPM cleanup, Approachable Concurrency readiness, Observation macro migration, SwiftData and App Intents audits, design system reuse, dependency cleanup, autonomous run-until-blocked execution, local per-task commits, and persistent incremental refactoring. This skill is Swift-only and rejects new ObservableObject-based patterns.
---

# Frontier-Modern SwiftUI Refactoring Methodology

A systematic workflow for refactoring AI-generated SwiftUI apps without changing behavior, visual appearance, or public interfaces. The minimum platform is iOS 18. Prefer the newest available Swift 6.x toolchain and Swift 6.2+ practices when they remain iOS 18 compatible. State management must prefer the Swift Observation macro system.

## Non-Negotiable Defaults

- SwiftUI is the primary UI framework.
- iOS 18 is the minimum supported platform.
- Swift 6 strict concurrency is the minimum compatibility target; Swift 6.2+ readiness is preferred.
- Prefer `@Observable`, `@Bindable`, `@State`, `@Environment`, explicit dependency injection, actors, and `@MainActor` isolation.
- Treat `ObservableObject`, `@Published`, `@StateObject`, and `@ObservedObject` as legacy patterns to migrate away from. Do not introduce them in new code.
- UIKit is allowed only for necessary interop or legacy migration.
- Preserve functionality, UI appearance, interaction behavior, and external APIs unless the user explicitly asks otherwise.
- Complete analysis before refactoring. Do not start implementation from a hunch.
- Keep all refactoring work traceable and independently rollbackable.
- When tasks exist, run autonomously until blocked and commit every completed task as described in [workspace/autonomous-execution.md](workspace/autonomous-execution.md).

## Modern Swift Defaults

- Audit Swift 6.2+ Approachable Concurrency settings, including strict concurrency, default actor isolation, caller-context async behavior, and intentional `@concurrent` work.
- UI executable targets may consider MainActor default isolation; Domain, Services, DesignSystem, and Infrastructure must not be blindly main-actor isolated.
- Treat `nonisolated`, `@concurrent`, `@preconcurrency`, `@unchecked Sendable`, and `nonisolated(unsafe)` as explicit audit points.
- Prefer Swift Testing for new unit tests while preserving existing tests unless migration is in scope.
- Prefer SwiftData boundaries that keep model lifetime, `ModelContext`, `@Query`, and Observation ownership explicit.
- Inventory App Intents, app entities, `IndexedEntity`, `Transferable`, Spotlight exposure, interactive snippets, and other system experiences when the app has user-visible entities or actions.
- Prefer typed navigation, `#Preview`, environment dependency injection, `@Bindable` edit flows, custom `Layout`, `contentTransition`, and `scrollPosition` over older wrapper-heavy patterns.
- Avoid unnecessary `AnyView`, broad `DispatchQueue` usage, stringly typed resources, and local UI wrappers that duplicate SwiftUI or DesignSystem capabilities.

## Recovery Entry

When the user says "continue refactoring" or a `.refactor/` directory exists:

1. Read `.refactor/README.md`.
2. Read `.refactor/tasks/master-plan.md`.
3. Read active task files in `.refactor/tasks/active/`.
4. Read the latest session log if the next action is unclear.
5. Continue with the next eligible task using the autonomous execution loop.

Use persisted files as the source of truth, not conversation memory.

## Autonomous Execution

Default to run until blocked. Do not wait for human supervision between dependency-ready tasks when the next safe action is derivable from `.refactor/`.

Use [workspace/autonomous-execution.md](workspace/autonomous-execution.md) for:

- Task eligibility and ordering.
- Stop conditions.
- Bounded verification repair.
- Local per-task code and state commits.
- Blocked-task handling and safe continuation.

## Workflow

```
Phase 0        Phase 1          Phase 2             Phase 3           Phase 4       Phase 5
Partition  ->  Identify Keys -> Architecture    ->  Module Deep   -> Execute   -> Verify
Project        Core features    Layer analysis      Analysis          Tasks        Finalize
Inventory      Entry paths      Dependencies        SwiftUI issues    Checkpoints  Cleanup
```

### Phase 0: Project Partition

Goal: understand the Swift project shape and inventory reusable resources.

Run targeted discovery:

```bash
swift --version
xcodebuild -version
rg --files -g '*.swift' -g 'Package.swift' -g '*.xcodeproj' -g '*.xcworkspace'
find . -maxdepth 3 -type d \( -name Sources -o -name Tests -o -name '*Tests' -o -name '*UITests' \)
rg '@main|struct .*: App|var body: some Scene|var body: some View' --glob '*.swift'
rg 'import SwiftUI|import Observation|import UIKit' --glob '*.swift'
rg 'swift-tools-version|swiftLanguageVersions|swiftSettings|defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
```

Inventory:

- Toolchain posture: Swift version, Xcode version, Swift language mode, strict concurrency, default actor isolation, upcoming features.
- Project containers: `Package.swift`, `.xcodeproj`, `.xcworkspace`, schemes, targets.
- App shell: `@main`, app scenes, navigation roots, dependency injection setup.
- Feature modules: screen groups, feature folders, SwiftPM targets, Xcode targets.
- Design system: reusable `View`s, `ViewModifier`s, `ButtonStyle`s, `LabelStyle`s, colors, typography, spacing, assets.
- State and models: `@Observable` models, `@State`, `@Bindable`, environment values, actors, services.
- Data and infrastructure: SwiftData, persistence, networking, logging, localization, analytics, permissions, configuration.
- System experiences: App Intents, app entities, Spotlight, controls, widgets, `Transferable`, interactive snippets.
- Tests and previews: Swift Testing, XCTest, UI tests, `#Preview`, preview fixtures, mock services.

Output: `.refactor/analysis/project-partition.md`.

### Phase 1: Key Identification

Goal: choose the core flows that need deep analysis.

Find SwiftUI and app entry points:

```bash
rg 'Button\(|\.task\(|\.onAppear\(|\.onChange\(|NavigationStack|sheet\(|fullScreenCover\(' --glob '*.swift'
rg 'AppIntent|EntityQuery|IndexedEntity|Transferable|Widget|ControlWidget|SnippetIntent|UIApplicationDelegateAdaptor' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
rg 'func .*\(|async throws|Task \{|await ' --glob '*.swift'
```

For each core feature record:

- User flow and expected behavior.
- Entry point with file and line.
- Views, models, services, actors, and targets involved.
- Data flow and async boundaries.
- Risk level and test coverage.

Output: `.refactor/analysis/key-identification.md`.

### Phase 2: Architecture Layer Analysis

Goal: find global Swift architecture problems before local cleanup.

Default layering:

```
App
Features
DesignSystem
Domain
Services
Infrastructure
```

Check:

- SwiftPM/Xcode target dependency direction.
- Feature-to-feature coupling.
- App or UI layer leaking into Domain or Infrastructure.
- DesignSystem depending on app-specific feature code.
- Services bypassing protocols or test seams.
- MainActor, default actor isolation, `nonisolated`, `@concurrent`, and actor boundaries.
- Conditional compilation scattered across feature code.
- SwiftData and App Intents boundaries crossing feature, service, or system-experience layers.

Output: `.refactor/analysis/architecture-report.md`.

Read [analysis/architecture-analysis.md](analysis/architecture-analysis.md) when performing this phase.

### Phase 3: Module Layer Analysis

Goal: deeply inspect each key SwiftUI feature and detect vibe-coding problems.

For each feature:

- Trace SwiftUI entry, state changes, async tasks, services, and rendering outputs.
- Compare local Views, Modifiers, styles, services, and models against the Phase 0 inventory.
- Detect duplicate implementations and inconsistent patterns.
- Detect legacy state management and plan migration to Observation macros.
- Check Swift 6 concurrency safety and actor isolation.
- Check Swift 6.2+ readiness: Approachable Concurrency settings, default isolation choices, and intentional concurrent execution.
- Check SwiftData, Swift Testing, App Intents, typed navigation, and modern SwiftUI API opportunities.
- Check visual behavior preservation risks.

Output one report per feature in `.refactor/analysis/modules/`, plus `.refactor/analysis/module-report.md`.

Read [analysis/deep-analysis.md](analysis/deep-analysis.md), [analysis/code-analysis.md](analysis/code-analysis.md), and [analysis/quality-assessment.md](analysis/quality-assessment.md) when performing this phase.

### Phase 4: Execute Refactoring

Execute architecture tasks before module tasks.

Task order:

1. Fix target dependency or layering violations.
2. Extract shared design-system/domain/service primitives.
3. Migrate legacy state patterns to Observation macros.
4. Replace duplicate local Views, Modifiers, styles, and services with shared resources.
5. Remove obsolete compatibility code.
6. Verify after each task, commit task-owned code changes, update `.refactor/` state, commit the state, then continue until blocked.

Use [patterns/refactor-patterns.md](patterns/refactor-patterns.md) and [strategies/dependency-sorting.md](strategies/dependency-sorting.md) to plan execution.

### Phase 5: Finalize and Verify

Run the strongest available validation for the project:

```bash
swift build
swift test
xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build
xcodebuild -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16' test
```

If configured, also run SwiftFormat and SwiftLint in check mode. For SwiftUI visual changes, verify previews, simulator flows, or screenshots for the affected screens.

Output:

- Final verification summary.
- Before/after comparison.
- Updated architecture diagrams if architecture changed.
- Archived completed tasks.

## Required Analysis Checklist

Before Phase 4 starts:

- [ ] Project containers, targets, schemes, and SwiftPM packages inventoried.
- [ ] Swift version, Xcode version, language mode, strict concurrency, and default actor isolation inventoried.
- [ ] App shell and core SwiftUI entry points identified.
- [ ] Design system and reusable resources inventoried.
- [ ] Services, protocols, actors, environment values, SwiftData models, App Intents, and system experiences inventoried.
- [ ] Architecture dependency direction checked.
- [ ] Legacy Observation migration candidates identified.
- [ ] Swift 6 concurrency risks identified.
- [ ] Swift 6.2+ Approachable Concurrency readiness assessed.
- [ ] Swift Testing, SwiftData, App Intents, and modern SwiftUI API coverage assessed.
- [ ] Module reports created for key features.
- [ ] Architecture and module tasks generated in `.refactor/tasks/master-plan.md`.

## Key Output Files

```
.refactor/
├── README.md
├── analysis/
│   ├── project-partition.md
│   ├── key-identification.md
│   ├── architecture-report.md
│   ├── module-report.md
│   └── modules/
├── tasks/
│   ├── master-plan.md
│   ├── active/
│   └── completed/
├── logs/
└── checkpoints/
```

## Related Documents

- [analysis/architecture-analysis.md](analysis/architecture-analysis.md)
- [analysis/code-analysis.md](analysis/code-analysis.md)
- [analysis/deep-analysis.md](analysis/deep-analysis.md)
- [analysis/quality-assessment.md](analysis/quality-assessment.md)
- [patterns/vibe-coding-problems.md](patterns/vibe-coding-problems.md)
- [patterns/refactor-patterns.md](patterns/refactor-patterns.md)
- [strategies/dependency-sorting.md](strategies/dependency-sorting.md)
- [strategies/partition-strategies.md](strategies/partition-strategies.md)
- [strategies/parallel-execution.md](strategies/parallel-execution.md)
- [workspace/workspace-spec.md](workspace/workspace-spec.md)
- [workspace/autonomous-execution.md](workspace/autonomous-execution.md)
- [workspace/task-lifecycle.md](workspace/task-lifecycle.md)
- [workspace/session-management.md](workspace/session-management.md)
- [workspace/recovery-guide.md](workspace/recovery-guide.md)
