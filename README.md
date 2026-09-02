# Jira → Figma → iOS Kiro Power

A reusable Kiro Power for implementing iOS features from Jira requirements and Figma designs.

## What it does

Use a prompt such as:

```text
Implement NCAPP-4521
```

The Power guides Kiro through:

1. Fetching Jira requirements and acceptance criteria.
2. Finding and reading linked Figma designs.
3. Inspecting the current iOS project before changing code.
4. Reusing existing SwiftUI/UIKit architecture and components.
5. Implementing UI and functionality.
6. Running build/tests and validating acceptance criteria.
7. Producing an implementation report with any remaining gaps.

## Repository structure

```text
.
├── README.md
├── POWER.md
├── plugin.json
├── mcp/
│   ├── jira.example.json
│   └── figma.example.json
├── skills/
│   ├── jira-to-ios/SKILL.md
│   ├── figma-to-ios/SKILL.md
│   ├── ios-architecture/SKILL.md
│   ├── design-system/SKILL.md
│   └── validation/SKILL.md
└── steering/
    ├── workflow.md
    ├── swift.md
    ├── swiftui.md
    ├── uikit.md
    └── testing.md
```

## Installation / usage

1. Add this Power to your Kiro setup using the Kiro Power mechanism supported by your Kiro version.
2. Configure Jira and Figma MCP servers in your Kiro environment. The example files in `mcp/` are templates only and intentionally contain no credentials.
3. Open an iOS repository in Kiro.
4. Ask Kiro to implement a Jira issue key.

## MCP credentials

Do not commit Jira, Figma, GitHub, or other access tokens to this repository. Keep credentials in the Kiro/MCP configuration mechanism provided by your environment.

## Project customization

This repository is intentionally generic. For a specific iOS codebase, add project-specific conventions to the `steering/` files or create additional skills. Avoid hard-coding company-private names, tokens, URLs, or source code into the reusable Power.

## Recommended workflow

The Power separates discovery from implementation. Kiro should inspect Jira, Figma, and the existing codebase first, produce an implementation plan, and then make changes. It should build/test after implementation and never claim success when validation fails.
