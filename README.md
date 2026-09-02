# Jira → Figma → iOS Kiro Power

A reusable Kiro Power for implementing iOS features from Jira requirements and Figma designs.

## What it does

The Power supports both a simple end-to-end workflow and focused commands for individual stages.

### Primary command

Use this for the normal end-to-end workflow:

```text
Implement NCAPP-4521
```

The Power should guide Kiro through Jira → Figma → iOS implementation → validation → Jira evidence → GitHub branch/PR → code review, subject to repository and team policies.

### Supported commands

Replace `NCAPP-4521` with the Jira issue key.

#### 1. Implement a feature

```text
Implement NCAPP-4521
```

Runs the full workflow: fetch Jira requirements, inspect linked Figma, analyze the existing iOS project, implement the feature, validate it, update Jira evidence, create/update the GitHub branch and PR when appropriate, and review the actual PR diff.

#### 2. Implement only

```text
Implement NCAPP-4521 only
```

Implements and validates the Jira issue without creating the PR or completing the Jira workflow. Keep the issue In Progress until later stages are explicitly completed.

#### 3. Validate an implementation

```text
Validate NCAPP-4521
```

Validates the current implementation against Jira acceptance criteria, Figma, build/test requirements, navigation and state handling, localization, accessibility, and other applicable project checks.

#### 4. Create a pull request

```text
Create PR for NCAPP-4521
```

Checks repository conventions, creates or uses the feature branch, commits/pushes the implementation when permitted, creates the GitHub PR, and records the PR reference in Jira.

#### 5. Review the pull request

```text
Review PR for NCAPP-4521
```

Reviews the actual GitHub PR metadata and diff against Jira acceptance criteria, Figma, architecture, code quality, tests, and validation requirements. Blocking findings must prevent completion.

#### 6. Fix review findings

```text
Fix review findings for NCAPP-4521
```

Addresses blocking review findings, then requires validation and another PR review before completion.

#### 7. Complete the Jira issue

```text
Complete NCAPP-4521
```

Runs the completion gate. Jira should move to Done only when required implementation, validation, PR, review, and approval/policy requirements are satisfied.

### Typical workflow

For most features, only this command is needed:

```text
Implement NCAPP-4521
```

The focused commands are useful when you want to stop at a specific stage or resume a workflow after manual changes or review feedback.

## Workflow gates

The Power must not advance a Jira issue simply because code was generated.

- Failed required validation blocks the move to In Review.
- Blocking code-review findings block completion.
- Do not fabricate screenshots, logs, test results, Jira proof, or review outcomes.
- Respect repository conventions, required human approvals, branch protection, CI, merge policies, and deployment controls.
- Inspect actual Jira transitions and GitHub repository state instead of assuming names or permissions.

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
8. Creating and reviewing a GitHub pull request when the workflow reaches that stage.

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
│   ├── validation/SKILL.md
│   ├── implementation-workflow/SKILL.md
│   └── code-review/SKILL.md
└── steering/
    ├── workflow.md
    ├── swift.md
    ├── swiftui.md
    ├── uikit.md
    └── testing.md
```

## Installation / usage

1. Add this Power to your Kiro setup using the Kiro Power mechanism supported by your Kiro version.
2. Configure Jira, Figma, and GitHub MCP integrations in your Kiro environment. The example files in `mcp/` are templates only and intentionally contain no credentials.
3. Open an iOS repository in Kiro.
4. Use the primary command or one of the focused commands above.

Kiro skills can also be exposed as slash commands when they are available as skills in the workspace/global skill directories. citeturn676898search2turn676898search3

## MCP credentials

Do not commit Jira, Figma, GitHub, or other access tokens to this repository. Keep credentials in the Kiro/MCP configuration mechanism provided by your environment.

## Project customization

This repository is intentionally generic. For a specific iOS codebase, add project-specific conventions to the `steering/` files or create additional skills. Avoid hard-coding company-private names, tokens, URLs, or source code into the reusable Power.

## Recommended workflow

The Power separates discovery from implementation. Kiro should inspect Jira, Figma, GitHub, and the existing codebase first, produce an implementation plan, and then make changes. It should build/test after implementation, review the actual PR diff, and never claim success when validation or review fails.
