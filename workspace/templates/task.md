# Swift Task Template

```markdown
---
id: task-{number}
title: {task title}
type: architecture | module | state-migration | modern-swift | data | intents | quality | verification
status: pending | in_progress | blocked | completed | cancelled
priority: high | medium | low
phase: {phase number}
dependencies: []
verification_status: pending | passed | failed | blocked | skipped
code_commit: pending
state_commit: pending
blocked_reason:
next_task:
created: {ISO time}
updated: {ISO time}
---

# Task: {Task Title}

## Goal
{One sentence}

## Scope
- Files/targets:
- Feature:
- Owned public API:
- Out of scope:

## Background
{Problem from analysis report}

## Target Pattern
- Platform: iOS 18
- Language: Swift 6 minimum, Swift 6.2+ readiness preferred
- State: Observation macros
- Concurrency:
- Approachable Concurrency:
- SwiftData:
- App Intents/System Experiences:
- Swift Testing:
- DesignSystem:

## Steps
- [ ] Confirm current behavior
- [ ] Record `git status --short`
- [ ] Apply scoped refactor
- [ ] Remove duplicate or legacy code
- [ ] Update tests/previews if needed
- [ ] Verify
- [ ] Commit task-owned code changes if applicable
- [ ] Update `.refactor/` state
- [ ] Commit `.refactor/` state changes

## Verification
| Check | Command | Result |
|-------|---------|--------|
| Build | `swift build` | pending |
| Test | `swift test` | pending |
| Xcode build | `xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build` | pending |
| Xcode test | `xcodebuild -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16' test` | pending |
| UI verification | Preview/simulator flow | pending |

## Progress Log
| Time | Operation | Result |
|------|-----------|--------|

## Change List
| File | Operation | Description |
|------|-----------|-------------|

## Commits
- Code commit: `pending`
- State commit: `pending`

## Blocker
- Verification status: `pending`
- Blocked reason:
- Safe next task:

## Notes
{Risks, skipped checks, or follow-up}
```
