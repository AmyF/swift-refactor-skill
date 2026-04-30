# Swift Parallel Execution Strategy

Use parallel work only when tasks have disjoint write scopes and independent verification paths.

## Safe Parallelization

Good candidates:

- Independent feature View cleanup after shared DesignSystem work is complete.
- Separate preview/test fixture updates for different features.
- Independent service call-site migrations after the protocol is stable.
- Documentation or report updates while implementation continues elsewhere.

Avoid parallelizing:

- Changes to the same observable model.
- Shared DesignSystem component API changes.
- App-level environment injection.
- SwiftPM/Xcode target dependency edits.
- Actor isolation changes used by multiple services.

## Task Split Rules

Each task must define:

- Owned files or target.
- Inputs from prior tasks.
- Expected public API after completion.
- Verification command.
- Rollback boundary.

## Parallel Plan Template

```markdown
## Parallel Batch: {Goal}

### Shared Prerequisite
- {task}: stabilize protocol / DesignSystem API / domain type

### Parallel Tasks
| Task | Owner Scope | Depends On | Verification |
|------|-------------|------------|--------------|
| task-A | Feature A Views | prerequisite | xcodebuild affected scheme |
| task-B | Feature B Views | prerequisite | xcodebuild affected scheme |

### Join Step
- Run full app build/test.
- Resolve integration issues.
- Update `.refactor/tasks/master-plan.md`.
```

## Verification

After joining parallel work:

```bash
swift build
swift test
xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build
```

Run simulator or preview verification for UI-affecting changes.
