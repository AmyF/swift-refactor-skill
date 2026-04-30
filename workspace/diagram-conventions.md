# Swift Diagram Conventions

Use Mermaid diagrams to make SwiftUI architecture and data flow easy to resume across sessions.

## Architecture Diagram

```mermaid
graph TB
    App["App"]
    Feature["Feature"]
    DesignSystem["DesignSystem"]
    Domain["Domain"]
    Services["Services"]
    Infrastructure["Infrastructure"]

    App --> Feature
    Feature --> DesignSystem
    Feature --> Domain
    Feature --> Services
    Services --> Domain
    Services --> Infrastructure
```

## Feature Call Flow

```mermaid
sequenceDiagram
    actor User
    participant View as SwiftUI View
    participant Model as @Observable Model
    participant Service as Service Protocol
    participant Actor as Actor/Infrastructure

    User->>View: Tap
    View->>Model: action()
    Model->>Service: await request()
    Service->>Actor: await boundary call
    Actor-->>Service: result
    Service-->>Model: value
    Model-->>View: observed state update
```

## State Ownership Diagram

```mermaid
graph TD
    Parent["Parent View @State model"] --> Child["Child View @Bindable model"]
    Parent --> Service["Injected Service"]
    Service --> Actor["Actor isolated storage"]
```

## Dependency Direction

Draw arrows from consumer to provider. Mark violations in red in reports when useful.

```mermaid
graph TB
    FeatureA --> FeatureB
    classDef violation fill:#ffd6d6,stroke:#c00
    class FeatureA,FeatureB violation
```
