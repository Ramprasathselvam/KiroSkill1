---
name: design-system
description: Map Figma visual properties to existing iOS design-system assets and reusable components instead of duplicating them.
---

# Design System

## Procedure

1. Search the repository for matching colors, fonts, icons, images, buttons, cards, controls, and layout components.
2. Prefer asset catalog colors/images and existing semantic tokens.
3. Prefer existing reusable components when their behavior and appearance match the requirement.
4. If no matching asset exists, identify the smallest project-level addition required.
5. Keep new constants localized and named semantically.

## Avoid

- Hard-coded colors when an equivalent asset/token exists.
- Duplicate buttons, sliders, cards, or navigation components.
- Copying Figma values into arbitrary constants without checking the project's design system.
- Broad design-system refactors during a feature implementation.
