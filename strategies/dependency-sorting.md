# Swift Dependency Sorting

Use dependency sorting to decide safe refactoring order across SwiftPM targets, Xcode targets, features, services, and design-system resources.

## Core Principle

Refactor providers before consumers. Verify the smallest affected target after each step.

## 1. List Involved Modules

```bash
rg 'target\(|executableTarget\(|testTarget\(|swiftSettings|defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
rg '^import ' --glob '*.swift'
rg 'ObservableObject|@Published|@StateObject|@ObservedObject' --glob '*.swift'
rg 'struct .*: View|ViewModifier|ButtonStyle|protocol .*Service|actor |@MainActor|@concurrent' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext|AppIntent|AppEntity|IndexedEntity|Transferable' --glob '*.swift'
```

Group files into:

- App shell
- Features
- DesignSystem
- Domain
- Services
- Infrastructure
- Tests and previews
- System experiences and persistence boundaries

## 2. Build the Dependency Graph

For each module or target, record:

- What it imports.
- Which features or targets import it.
- Whether it owns state, UI, domain models, services, or infrastructure.
- Which tests or previews rely on it.

Dependency examples:

```
App -> Features
Features -> DesignSystem
Features -> Domain
Features -> Services
Services -> Domain
Services -> Infrastructure
Tests -> Domain / Services / Features
```

## 3. Sort Refactoring Work

Recommended order:

1. Toolchain, Swift language mode, strict concurrency, and default actor isolation posture.
2. Domain types and shared value models.
3. SwiftData persistence boundaries and service protocols.
4. Service actors and infrastructure implementations.
5. DesignSystem resources.
6. App Intents and system experience boundaries.
7. Observable feature models.
8. Feature Views, typed navigation, and modern SwiftUI API adoption.
9. Swift Testing, previews, and cleanup.

When migrating legacy state, update the model owner first, then child bindings, then previews/tests.

## 4. Handle Cycles

If Feature A and Feature B import each other:

- Extract shared domain types to Domain.
- Extract shared UI to DesignSystem.
- Extract shared behavior to a service protocol.
- Move navigation coordination to App or a parent feature.

If a service depends on UI:

- Move UI-specific formatting to Features or DesignSystem.
- Keep service output domain-oriented.

## 5. Execution Plan Template

```markdown
## Execution Plan: {Goal}

### Phase 1: Shared Foundation
- task-001: Record Swift 6.2 readiness and update Domain/service protocol
- Verify: `swift build` or target-specific Xcode build

### Phase 2: Providers
- task-002: Update actor/service or SwiftData boundary
- task-003: Update DesignSystem resource or App Intents entity boundary
- Verify: affected package/target

### Phase 3: Consumers
- task-004: Migrate feature model to Observation
- task-005: Replace duplicate Views with modern SwiftUI APIs
- Verify: app build and feature tests

### Phase 4: Cleanup
- task-006: Remove legacy state, unsafe concurrency shims, and local duplicates
- Verify: full build/test
```
