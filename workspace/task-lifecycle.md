# Swift Refactor Task Lifecycle

Tasks are the unit of rollback and verification for SwiftUI-first refactoring.

## Statuses

- `pending`
- `in_progress`
- `blocked`
- `completed`
- `cancelled`

## Create a Task

1. Assign an ID such as `task-A001` or `task-M001`.
2. Define owned files, targets, or feature scope.
3. Record dependencies.
4. Define the target Swift pattern before editing.
5. Add the strongest practical verification command.
6. Add autonomy metadata: `verification_status`, `code_commit`, `state_commit`, `blocked_reason`, and `next_task`.
7. Add the task to `.refactor/tasks/master-plan.md`.

## Execute a Task

1. Re-read the related analysis report.
2. Confirm no dependency task is incomplete.
3. Record `git status --short` in the task log.
4. Make the smallest coherent change.
5. Verify with SwiftPM or Xcode.
6. Commit task-owned code changes with `task-A001: refactor <short goal>`, if the task changed code.
7. Update the task log and `.refactor/` status.
8. Move completed tasks to `.refactor/tasks/completed/`.
9. Commit `.refactor/` state changes with `task-A001: update refactor state`.
10. Continue to the next dependency-ready task until blocked.

See [autonomous-execution.md](autonomous-execution.md) for stop conditions, scoped repair, and commit rules.

## Verification Expectations

Use the narrowest useful command first, then a wider command before completion.

```bash
swift build
swift test
xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build
xcodebuild -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16' test
```

For SwiftUI visual changes, verify affected previews, simulator flows, or screenshots.

## Completion Criteria

- Code builds under Swift 6.
- Swift 6.2+ readiness risks are recorded or resolved.
- Relevant tests or previews pass.
- No new legacy state pattern was introduced.
- UI and behavior remain unchanged unless explicitly scoped.
- Deprecated duplicate code is removed.
- `.refactor/README.md` and the master plan are updated.
- Code changes are committed, or `code_commit` is recorded as `none` for analysis-only/state-only tasks.
- `.refactor/` state changes are committed and `state_commit` is recorded.

## Blocked Task Record

```markdown
## Blocked
- Time:
- Reason:
- Required decision or dependency:
- Safe next task:
- Verification status:
```
