# Swift Analysis Report Template

```markdown
# Analysis Report: {Subject}

## Basic Info
- Date:
- Type: Project | Architecture | Feature | Quality
- Platform: iOS 18
- Language: Swift 6 minimum, Swift 6.2+ readiness preferred
- Scope:

## Modern Swift Posture
| Area | Current | Recommendation |
|------|---------|----------------|
| Swift/Xcode | | |
| Strict concurrency | | |
| Default actor isolation | | |
| Approachable Concurrency | | |
| Swift Testing | | |
| SwiftData | | |
| App Intents/System Experiences | | |

## Project Shape
| Container/Target | Responsibility | Notes |
|------------------|----------------|-------|

## Entry Points
| Entry | Location | Trigger | Frequency |
|-------|----------|---------|-----------|

## Call Flow
```text
{SwiftUI entry -> observable model -> service -> actor/infrastructure -> render}
```

## State and Observation
| State Owner | Pattern | Lifetime | Recommendation |
|-------------|---------|----------|----------------|

## Dependencies
| Consumer | Provider | Relationship | Issue |
|----------|----------|--------------|-------|

## Resource Reuse
| Local Implementation | Existing Resource | Recommendation |
|----------------------|-------------------|----------------|

## Swift 6.2+ Concurrency
| Location | Risk | Recommendation |
|----------|------|----------------|

## Data and System Experiences
| Area | Current | Gap |
|------|---------|-----|
| SwiftData | | |
| App Intents | | |
| Spotlight/Transferable | | |

## Quality Issues
| Issue | Location | Priority | Fix |
|-------|----------|----------|-----|

## Tests and Previews
| Area | Existing Coverage | Gap |
|------|-------------------|-----|

## Refactoring Recommendations
1. ...
```
