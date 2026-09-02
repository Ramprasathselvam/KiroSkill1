# Jira → Figma → iOS Power

## Purpose

This Power turns a Jira issue plus its linked Figma design into a disciplined iOS implementation and delivery workflow.

## Activation

Use when the user asks to implement, build, develop, or update an iOS feature from a Jira issue, especially when the issue contains a Figma link.

Typical prompts:

- `Implement NCAPP-4521`
- `Build the feature from JIRA NCAPP-4521`
- `Implement this Jira ticket and match the Figma design`

## Operating rules

### 1. Discovery before coding

Never start implementation immediately. First gather the Jira issue, linked design information, and relevant existing project code.

### 2. Jira

Extract the issue key, title, description, acceptance criteria, comments, attachments, linked issues, labels, and design links. Treat acceptance criteria as the functional source of truth unless the user explicitly overrides them.

### 3. Figma

When a Figma URL is available, inspect the relevant frame/component and determine layout, spacing, typography, colors, icons, states, interactions, and reusable components. Do not assume a visual detail that cannot be observed.

### 4. Existing iOS code

Search the repository before creating new architecture or components. Prefer the closest existing View/ViewController, ViewModel/state owner, coordinator/navigation flow, API client, model, design-system asset, and reusable UI component.

### 5. Plan

Before editing, produce a concise plan covering files to change, reused components, API/data changes, navigation, state handling, and tests. Ask for approval when the change is broad, destructive, ambiguous, or affects shared infrastructure. For routine feature work, proceed after discovery and planning.

### 6. Implementation

Implement functional behavior and UI together. Respect the project's existing Swift, SwiftUI, UIKit, localization, accessibility, networking, and testing conventions. Avoid introducing dependencies unless required.

### 7. Delivery lifecycle

For a Jira-driven implementation, follow the gated lifecycle in `skills/implementation-workflow/SKILL.md`:

`To Do` → `In Progress` → implementation → validation → `In Review` → pull request → code review → `Done`

Do not advance Jira status merely because code was generated.

### 8. Validation gate

Build the relevant target and run relevant tests. Re-check every Jira acceptance criterion. Compare the implementation with the Figma design when visual tooling is available. Clearly report anything that could not be verified.

A failed required validation blocks the transition to `In Review`.

### 9. Jira proof

After successful validation, add an evidence-based implementation and validation summary to the Jira issue before or as part of the `In Review` transition. Never fabricate screenshots, logs, test results, or other proof.

### 10. Pull request and review

Create a Jira-linked feature branch and pull request using the project's existing conventions. Use `skills/code-review/SKILL.md` for the review gate. Blocking review findings prevent the Jira issue from moving to `Done`.

If the repository requires human approval, merge, CI, or deployment before completion, do not bypass that requirement.

## Skills

- `jira-to-ios`: Jira discovery and requirements extraction.
- `figma-to-ios`: Figma inspection and design translation.
- `ios-architecture`: repository-aware Swift/SwiftUI/UIKit implementation.
- `design-system`: reuse project assets and components.
- `implementation-workflow`: Jira status gates, validation, proof, pull request, and completion lifecycle.
- `validation`: build, test, acceptance-criteria, and design verification.
- `code-review`: pull request quality gate before Jira completion.

## Security

Never request, print, commit, or hard-code access tokens or secrets. MCP authentication belongs in the user's Kiro/MCP environment.
