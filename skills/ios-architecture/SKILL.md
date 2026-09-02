---
name: ios-architecture
description: Analyze and implement features within an existing Swift, SwiftUI, or UIKit project while preserving its architecture and conventions.
---

# iOS Architecture

## Before editing

Search for the closest existing feature and identify:

- ViewController/View
- ViewModel/state owner
- Model/domain types
- Networking/API client
- Navigation/coordinator
- Reusable UI components
- Asset/design-system usage
- Localization
- Tests

## Implementation rules

1. Reuse existing architecture instead of introducing a parallel pattern.
2. Reuse existing networking and dependency injection mechanisms.
3. Reuse existing navigation and coordinator patterns.
4. Prefer the project's existing SwiftUI/UIKit boundary decisions.
5. Keep state ownership correct: use `@StateObject`, `@ObservedObject`, and `@Binding` according to lifecycle/ownership rather than as interchangeable annotations.
6. Preserve iOS deployment-target compatibility; do not introduce APIs newer than the project's minimum OS without an availability strategy.
7. Avoid unnecessary refactors outside the Jira scope.
8. Keep business logic out of views when the project uses ViewModels/services.
9. Add or update unit/UI tests following existing test conventions.
