# Swift Refactoring Patterns

Standard fixes for frontier-modern SwiftUI-first iOS 18 / Swift 6.2-ready projects.

## Pattern 1: Adopt DesignSystem Resources

Use when a feature has local UI pieces equivalent to shared Views, modifiers, styles, assets, or tokens.

Steps:

1. Identify the local implementation and the shared equivalent.
2. Compare visual output, interaction, accessibility, and customization needs.
3. Replace local usage with the shared resource.
4. Extract missing reusable behavior into DesignSystem only when multiple features need it.
5. Delete the local duplicate after verification.

Example:

```swift
// Before
struct LocalErrorBanner: View {
    let message: String

    var body: some View {
        Text(message)
            .padding()
            .background(.red.opacity(0.1))
            .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}

// After
ErrorBanner(message: message)
```

## Pattern 2: Migrate Legacy State to Observation

Use when legacy patterns appear: `ObservableObject`, `@Published`, `@StateObject`, or `@ObservedObject`.

Steps:

1. Determine model lifetime and ownership.
2. Convert the model to `@Observable`.
3. Store view-owned model instances in `@State`.
4. Use `@Bindable` for child edit flows.
5. Replace publisher-driven side effects with explicit async methods or computed state.
6. Verify Swift 6 concurrency diagnostics.

Example:

```swift
// Target shape
@Observable
final class LibraryModel {
    var albums: [Album] = []
    var isLoading = false

    func refresh(using service: LibraryService) async throws {
        isLoading = true
        defer { isLoading = false }
        albums = try await service.albums()
    }
}

struct LibraryView: View {
    @State private var model = LibraryModel()

    var body: some View {
        AlbumList(albums: model.albums)
            .task {
                try? await model.refresh(using: service)
            }
    }
}
```

## Pattern 3: Unify Services Behind Protocols

Use when features directly access infrastructure or duplicate clients.

Steps:

1. Define the smallest service protocol needed by the feature.
2. Move concrete implementation into Services or Infrastructure.
3. Inject dependencies through initializers or environment.
4. Use test doubles in tests and previews.
5. Remove direct infrastructure calls from Views.

Example:

```swift
protocol AlbumService: Sendable {
    func albums() async throws -> [Album]
}

actor RemoteAlbumService: AlbumService {
    func albums() async throws -> [Album] {
        // network boundary
    }
}
```

## Pattern 4: Extract Stable SwiftUI Subviews

Use when a View body grows too large or mixes many responsibilities.

Steps:

1. Identify stable visual sections.
2. Extract subviews with explicit immutable inputs.
3. Pass `@Binding` or `@Bindable` only when the child mutates state.
4. Keep side effects in `.task`, actions, models, or services.
5. Add previews for important visual states.

Example:

```swift
struct AlbumRow: View {
    let album: Album

    var body: some View {
        HStack {
            ArtworkView(image: album.artwork)
            Text(album.title)
        }
    }
}
```

## Pattern 5: Clarify Actor and MainActor Boundaries

Use when mutable shared state, UI state, and async services are mixed.

Steps:

1. Inspect strict concurrency, default actor isolation, and upcoming feature settings.
2. Mark UI-facing observable models `@MainActor` only when their mutations must occur on the main actor.
3. Do not blindly apply MainActor default isolation to Domain, Services, DesignSystem, or Infrastructure.
4. Move non-UI mutable shared resources into actors.
5. Use `@concurrent` only for intentionally concurrent work.
6. Ensure `nonisolated`, `@preconcurrency`, and unchecked sendability have documented reasons.
7. Ensure values crossing isolation boundaries are safely `Sendable`.
8. Replace untracked work with structured async APIs.

## Pattern 6: Adopt Approachable Concurrency Deliberately

Use when a project is moving toward Swift 6.2+ concurrency defaults.

Steps:

1. Record the current Swift toolchain, language mode, strict concurrency, and default actor isolation.
2. Choose isolation per target: UI executable targets may use MainActor default isolation; reusable libraries should remain explicitly isolated.
3. Replace `DispatchQueue` and detached tasks with structured async APIs.
4. Use actors for mutable shared resources.
5. Add tests around cancellation, reentrancy, and cross-actor calls.

## Pattern 7: Prefer Swift Testing for New Unit Coverage

Use when adding or modernizing non-UI tests.

Steps:

1. Preserve existing test coverage.
2. Prefer Swift Testing for new Domain, Services, and model tests.
3. Keep UI automation in the existing UI test framework.
4. Use fixtures and mock services that respect `Sendable` and actor isolation.
5. Document XCTest-to-Swift-Testing migration only when explicitly in scope.

## Pattern 8: Clean SwiftData Boundaries

Use when SwiftData models, contexts, and View state are tangled.

Steps:

1. Identify `ModelContainer`, `ModelContext`, `@Model`, and `@Query` ownership.
2. Keep context access near persistence or feature boundaries.
3. Avoid making persistence models the only domain model when a separate value model keeps layers cleaner.
4. Keep SwiftData lifetimes compatible with Observation and SwiftUI rendering.
5. Verify tests with isolated stores or fixtures.

## Pattern 9: Audit App Intents and System Experiences

Use when app actions or entities should participate in Shortcuts, Spotlight, controls, widgets, or interactive snippets.

Steps:

1. Identify stable app entities and user-visible actions.
2. Ensure App Intents call the same services as SwiftUI flows.
3. Add or clean up `AppEntity`, `EntityQuery`, `IndexedEntity`, and `Transferable` boundaries where useful.
4. Keep permission and error behavior consistent with the in-app flow.
5. Verify intent execution does not bypass app architecture.

## Pattern 10: Replace Wrapper-Heavy SwiftUI with Modern APIs

Use when code uses type erasure, imperative wrappers, or old navigation patterns where native SwiftUI APIs are now enough.

Steps:

1. Prefer typed navigation and explicit route state.
2. Replace unnecessary `AnyView` with generics, `@ViewBuilder`, or dedicated subviews.
3. Use custom `Layout`, `contentTransition`, and `scrollPosition` where they simplify local wrappers.
4. Keep UIKit interop only for missing SwiftUI capabilities.
5. Verify visual behavior with previews or simulator screenshots.

## Pattern 11: Rebuild Target and Feature Boundaries

Use when SwiftPM or Xcode target dependencies are unclear.

Steps:

1. Map current target dependencies.
2. Define public APIs for Domain, DesignSystem, Services, and Features.
3. Move platform-specific code to Infrastructure or adapters.
4. Remove feature-to-feature imports.
5. Verify each target and then the full app.

## Verification Checklist

After each refactoring task:

```bash
swift build
swift test
xcodebuild -scheme <Scheme> -destination 'generic/platform=iOS Simulator' build
xcodebuild -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16' test
```

If present, run SwiftFormat and SwiftLint in check mode. For UI changes, verify the affected previews or simulator flows.
