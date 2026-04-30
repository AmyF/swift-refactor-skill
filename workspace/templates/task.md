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
- [ ] Apply scoped refactor
- [ ] Remove duplicate or legacy code
- [ ] Update tests/previews if needed
- [ ] Verify
- [ ] Update `.refactor/` state

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

## Notes
{Risks, skipped checks, or follow-up}
```
