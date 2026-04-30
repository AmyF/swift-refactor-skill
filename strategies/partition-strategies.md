# Swift Project Partition Strategies

Partition SwiftUI projects by runtime boundary, target boundary, and feature responsibility.

## 1. Project Container Partition

```bash
swift --version
xcodebuild -version
rg --files -g 'Package.swift' -g '*.xcodeproj' -g '*.xcworkspace'
find . -maxdepth 3 -type d \( -name Sources -o -name Tests -o -name '*Tests' -o -name '*UITests' \)
rg '@main|struct .*: App' --glob '*.swift'
rg 'swift-tools-version|swiftSettings|defaultIsolation|enableUpcomingFeature|StrictConcurrency' Package.swift
```

Record:

- App target.
- Framework/library targets.
- Test and UI test targets.
- SwiftPM packages.
- Xcode schemes.
- Swift language mode, strict concurrency, default actor isolation, and upcoming features.

## 2. Layer Partition

Use this default map unless the repository already has a stronger convention:

| Layer | Responsibility |
|-------|----------------|
| App | App entry, scenes, composition, environment injection |
| Features | Screens, feature models, navigation, user flows |
| DesignSystem | Shared Views, styles, modifiers, tokens, assets |
| Domain | Business types, value models, pure logic |
| Services | Use-case protocols, app services, actors |
| Infrastructure | External APIs, persistence, platform adapters |

## 3. Feature Partition

Identify feature roots:

```bash
rg 'NavigationStack|navigationDestination|sheet\(|fullScreenCover\(' --glob '*.swift'
rg 'struct .*View: View' --glob '*.swift'
rg '@Observable|@State|@Bindable|@Environment' --glob '*.swift'
rg 'SwiftData|@Model|@Query|ModelContainer|ModelContext' --glob '*.swift'
rg 'AppIntent|AppEntity|IndexedEntity|Transferable|SnippetIntent' --glob '*.swift'
```

For each feature:

- Entry View.
- State owner.
- Services used.
- Domain models touched.
- DesignSystem dependencies.
- SwiftData and App Intents boundaries.
- Tests and previews.

## 4. Resource Inventory

Inventory reusable resources:

```bash
rg 'struct .*: View|ViewModifier|ButtonStyle|LabelStyle|ShapeStyle' --glob '*.swift'
rg 'extension View|extension Color|extension Font' --glob '*.swift'
rg 'Assets.xcassets|Localizable' -n .
rg '#Preview|PreviewProvider|Testing|@Test|XCTestCase' --glob '*.swift'
rg 'Layout|contentTransition|scrollPosition|AnyView' --glob '*.swift'
```

Output sections:

- DesignSystem Views and modifiers.
- Style tokens and assets.
- Services and protocols.
- Observable models and environment values.
- SwiftData models and persistence containers.
- App Intents, app entities, Spotlight, and transfer models.
- Preview fixtures and mocks.
- Swift Testing and XCTest coverage.

## 5. Partition Report Template

```markdown
# Project Partition

## Containers and Targets
| Container | Targets/Schemes | Notes |
|-----------|-----------------|-------|

## Modern Swift Posture
| Area | Value | Notes |
|------|-------|-------|
| Swift/Xcode | | |
| Strict concurrency | | |
| Default actor isolation | | |
| Swift Testing | | |
| SwiftData | | |
| App Intents/System Experiences | | |

## Layers
| Layer | Paths/Targets | Responsibility |
|-------|---------------|----------------|

## Reusable Resources
| Resource | Location | Consumers | Notes |
|----------|----------|-----------|-------|

## Core Features
| Feature | Entry | State Owner | Services | Tests/Previews |
|---------|-------|-------------|----------|----------------|
```
