# Frontier-Modern SwiftUI Refactor Skill

A Swift-only refactoring methodology for AI-generated SwiftUI apps targeting **iOS 18** and **Swift 6.2+ readiness**.

The skill helps an agent analyze and refactor SwiftUI projects by finding duplicate implementations, unused design-system resources, legacy state patterns, inconsistent services, weak concurrency boundaries, SwiftData lifetime issues, App Intents exposure gaps, and other common issues caused by context-fragmented AI coding.

## What It Optimizes For

- SwiftUI app architecture and feature boundaries
- iOS 18 compatibility with Swift 6.2+ modern defaults
- Approachable Concurrency, strict concurrency, and default actor isolation audits
- Observation macro state management with `@Observable` and `@Bindable`
- SwiftData, Swift Testing, App Intents, Spotlight, `Transferable`, and system experiences
- Design system reuse: Views, modifiers, styles, assets, colors, typography, spacing
- Xcode and SwiftPM target dependency cleanup
- Incremental, persistent refactoring through `.refactor/`
- Unattended task execution that runs until blocked and creates local per-task commits

## Hard Defaults

- SwiftUI first.
- iOS 18 minimum.
- Swift 6 language mode minimum; Swift 6.2+ readiness preferred.
- New code must not introduce `ObservableObject`, `@Published`, `@StateObject`, or `@ObservedObject`.
- Legacy state patterns are detected and migrated toward Observation macros.
- Domain, Services, DesignSystem, and Infrastructure must not be blindly main-actor isolated.
- Analysis must finish before refactoring begins.

## Usage

Install this folder as a Codex/Cursor skill, then ask for tasks such as:

```text
Refactor this SwiftUI app using the frontier-modern Swift methodology.
Run the autonomous Swift refactor loop until blocked.
Analyze this iOS 18 Swift 6.2-ready project architecture.
Clean up duplicate SwiftUI views and migrate legacy state to Observation.
Audit SwiftData, Swift Testing, App Intents, and Approachable Concurrency readiness.
Continue refactoring.
```

## Workflow

```
Project partition -> Key feature identification -> Architecture analysis
-> Module deep analysis -> Execute refactoring -> Final verification
```

The skill stores durable state in `.refactor/` so later agents can continue from the last checkpoint. During execution it defaults to running until blocked, with separate local commits for task code changes and `.refactor/` state updates.

## Project Layout

```
swift-refactor/
├── SKILL.md
├── analysis/
├── patterns/
├── strategies/
└── workspace/
```

## Key Documents

- [SKILL.md](SKILL.md) - main entry point
- [Deep Analysis](analysis/deep-analysis.md) - SwiftUI feature analysis
- [Problem Classification](patterns/vibe-coding-problems.md) - Swift-specific issues
- [Refactoring Patterns](patterns/refactor-patterns.md) - standard Swift fixes
- [Workspace Spec](workspace/workspace-spec.md) - persistent `.refactor/` format
- [Autonomous Execution](workspace/autonomous-execution.md) - run-until-blocked task loop and commit protocol

## License

MIT
