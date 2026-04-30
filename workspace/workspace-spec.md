# Swift Refactor Workspace Specification

The `.refactor/` workspace stores durable context for frontier-modern SwiftUI-first iOS 18 / Swift 6.2-ready refactoring.

## Directory Layout

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

## README.md

The recovery entry point. Keep it current after every session.

```markdown
# Swift Refactor Status

## Project
- Name:
- Platform: iOS 18
- Language: Swift 6 minimum, Swift 6.2+ readiness preferred
- UI: SwiftUI
- State direction: Observation macros
- Modern posture: Approachable Concurrency, Swift Testing, SwiftData, App Intents

## Progress
- Current phase:
- Total progress:
- Active task:
- Last verified command:

## Current Architecture Notes
- App:
- Features:
- DesignSystem:
- Domain:
- Services:
- Infrastructure:

## Next Step
{one concrete next action}
```

## Analysis Artifacts

### project-partition.md

Must include containers, targets, schemes, SwiftPM packages, toolchain settings, reusable resources, system experiences, and feature map.

### key-identification.md

Must list core SwiftUI flows with entries, state owners, services, and tests/previews.

### architecture-report.md

Must list target dependencies, layer violations, coupling issues, Swift 6.2 readiness, and concurrency boundary risks.

### module-report.md

Must summarize feature-level findings, including DesignSystem reuse, Observation migration, duplicate services, SwiftData/App Intents coverage, and Swift 6.2 readiness.

## Task Files

Task files live in `.refactor/tasks/active/` until complete.

Required fields:

- Goal.
- Owned files/targets.
- Dependencies.
- Implementation steps.
- Verification command.
- Rollback notes.
- Progress log.

## Checkpoints

Create a checkpoint after each architecture phase or meaningful module batch.

Checkpoint record:

- Git ref.
- Completed tasks.
- Verification results.
- Known risks.
- Next recommended task.

## Verification Commands

Prefer the strongest command available:

```bash
swift build
swift test
xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build
xcodebuild -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16' test
```

If configured, include SwiftFormat and SwiftLint in check mode.
