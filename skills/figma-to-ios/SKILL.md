---
name: figma-to-ios
description: Inspect a linked Figma design and translate observed design details into an existing iOS codebase. Use when a Jira issue contains a Figma URL or the user provides a Figma design.
---

# Figma to iOS

## Procedure

1. Open the linked Figma resource with the configured Figma MCP integration.
2. Identify the exact feature frame, relevant variants, components, and states.
3. Inspect layout hierarchy, spacing, dimensions, typography, colors, borders, corner radius, shadows, icons, images, controls, and interaction states.
4. Identify existing project components that can represent each design element.
5. Translate the observed design into SwiftUI or UIKit according to the existing screen architecture.
6. Prefer design-system assets and components over new hard-coded values.
7. Record any design detail that could not be inspected or verified.

## Fidelity rules

- Match observed spacing and sizing rather than approximating from memory.
- Reuse existing project assets when they correspond to the design.
- Do not replace a project design-system token with a raw color without a reason.
- Support loading, disabled, error, empty, selected, and other states when represented by the design or required by Jira.
- Do not claim pixel-perfect fidelity unless the available tooling actually allowed visual verification.
