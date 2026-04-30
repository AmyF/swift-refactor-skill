# Swift Refactor Recovery Guide

Use this when resuming an existing `.refactor/` workspace.

## Recovery Steps

1. Read `.refactor/README.md`.
2. Read `.refactor/tasks/master-plan.md`.
3. Read all files in `.refactor/tasks/active/`.
4. Read the latest log in `.refactor/logs/` if the next action is unclear.
5. Confirm the project still targets iOS 18 and remains Swift 6.2-ready.
6. Report status and continue with the safest active task.

## Status Report Template

```markdown
## Swift Refactor Status

### Overall
- Project:
- Platform: iOS 18
- Language: Swift 6 minimum, Swift 6.2+ readiness preferred
- Phase:
- Progress:

### Active Task
- ID:
- Goal:
- Last completed step:
- Next step:

### Current Risks
- Observation migration:
- Swift 6.2+ concurrency:
- SwiftData:
- App Intents/System Experiences:
- UI behavior:

### Verification
- Last command:
- Result:
```

## Recovery Safety

- Do not restart analysis unless the persisted analysis is missing or stale.
- Do not introduce legacy state patterns.
- Do not change UI behavior without an explicit task.
- If active work is ambiguous, continue with read-only analysis and update the recovery report.
