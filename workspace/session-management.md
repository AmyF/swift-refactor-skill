# Swift Refactor Session Management

A session is one uninterrupted period of analysis or implementation.

## Session Start

1. Check for `.refactor/`.
2. If it exists, read `.refactor/README.md`, the master plan, active tasks, and latest log.
3. If it does not exist, start Phase 0 project partition.
4. Report current phase briefly, then continue with the next eligible task without waiting for supervision.

## During the Session

After each meaningful step, update:

- Active task progress.
- Session log.
- Verification result.
- Newly discovered risks.
- Next action.
- Commit hashes for completed task code and `.refactor/` state.

## Session Log Template

```markdown
# Session Log

## Metadata
- Date:
- Project:
- Platform: iOS 18
- Language: Swift 6 minimum, Swift 6.2+ readiness preferred

## Work Completed
| Time | Operation | Result |
|------|-----------|--------|

## Verification
| Command | Result | Notes |
|---------|--------|-------|
| swift build | | |
| swift test | | |
| xcodebuild build | | |
| xcodebuild test | | |

## State Notes
- Observation migration:
- Swift 6.2+ concurrency:
- SwiftData:
- App Intents/System Experiences:
- Swift Testing:
- DesignSystem reuse:
- UI verification:

## Next
{one concrete continuation point}
```

## Session End

Before stopping because all work is complete or a real blocker exists:

1. Save the session log.
2. Update active task files.
3. Update `.refactor/tasks/master-plan.md`.
4. Update `.refactor/README.md`.
5. Record any verification not run and why.
6. Commit `.refactor/` state if it changed and the task state is complete.

## Recovery Rule

New agents must be able to continue from `.refactor/` files alone. They should resume the autonomous execution loop, not ask for manual task handoff, unless the persisted state records a blocker requiring human input.
