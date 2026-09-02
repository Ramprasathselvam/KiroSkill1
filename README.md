# Jira → Figma → iOS Kiro Power

A reusable Kiro Power for implementing iOS features from Jira requirements and Figma designs.

## What it does

The Power supports implementation-first work and explicit follow-up commands for validation, pull requests, review, and completion.

### Primary command

Use this to implement a Jira ticket and then stop:

```text
Implement NCAPP-4521
```

Before changing code, the Power fetches the Jira issue, checks its status, and moves `To Do` → `In Progress` when needed. It then creates or reuses the appropriate ticket branch, discovers the linked Figma design and existing iOS architecture, implements the ticket, captures simulator evidence, uploads the screenshot to Jira, adds an implementation comment, and stops.

It does **not** automatically run the full validation gate, move Jira to `In Review`, create a pull request, review the PR, or move Jira to `Done`.

### Supported commands

Replace `NCAPP-4521` with the Jira issue key.

#### 1. Implement a ticket

```text
Implement NCAPP-4521
```

Implementation-only workflow:

`Jira status → To Do to In Progress → branch → Jira/Figma discovery → iOS implementation → Simulator screenshot → Jira evidence/comment → STOP`

During implementation, the Power should:

- Move a `To Do` ticket to `In Progress` before modifying code.
- Determine whether the ticket is a Feature or Bug.
- Create or reuse the correct ticket branch.
- Inspect the linked Figma design and existing iOS architecture.
- Implement the requested change without unrelated refactoring.
- Run the implemented feature in the iOS Simulator when simulator access is available.
- Capture a relevant simulator screenshot as evidence.
- Upload the actual screenshot to the Jira issue and reference it in the implementation comment.
- Add an evidence-based implementation comment to Jira.
- Stop after these implementation steps.

If simulator execution or Jira attachment upload is unavailable, the Power must report the blocked evidence step and must not claim that a screenshot was captured or uploaded.

#### 2. Validate an implementation

```text
Validate NCAPP-4521
```

Runs the applicable build, unit/UI tests, Jira acceptance-criteria checks, navigation/state checks, localization/accessibility checks, and Figma validation.

#### 3. Create a pull request

```text
Create PR for NCAPP-4521
```

Creates or uses the ticket branch, commits/pushes the intended changes when permitted, creates the GitHub PR, and links the PR to Jira.

#### 4. Review the pull request

```text
Review PR for NCAPP-4521
```

Reviews the actual GitHub PR diff against Jira acceptance criteria, Figma, architecture, code quality, tests, and validation requirements.

#### 5. Fix review findings

```text
Fix review findings for NCAPP-4521
```

Addresses review findings, then requires validation and another review before completion.

#### 6. Complete the Jira issue

```text
Complete NCAPP-4521
```

Runs the completion gate and moves Jira to `Done` only when the required validation, PR, review, and approval/policy requirements are satisfied.

### Branching strategy

Create a dedicated ticket branch for implementation.

| Jira ticket type | Branch pattern |
|---|---|
| Feature | `feature/<JIRA-KEY>-<short-description>` |
| Bug | `bug/<JIRA-KEY>-<short-description>` |

Examples:

```text
feature/NCAPP-4521-battery-departure
bug/NCAPP-4522-battery-limit
```

Rules:

- Determine Feature vs Bug from the Jira issue type and metadata.
- Use lowercase `feature/` or `bug/` prefixes.
- Keep the Jira issue key unchanged.
- Use a short kebab-case description.
- Follow an existing repository-specific branch convention when one is defined.
- Reuse an existing matching ticket branch rather than creating a duplicate.
- Base new branches on the repository's normal development base branch.
- Do not commit directly to the protected/default branch for ticket work when a ticket branch is required.

### Typical workflow

For implementation:

```text
Implement NCAPP-4521
```

Then, only when requested:

```text
Validate NCAPP-4521
Create PR for NCAPP-4521
Review PR for NCAPP-4521
Complete NCAPP-4521
```

## Workflow gates

The Power must not advance a Jira issue simply because code was generated.

- `To Do` must become `In Progress` before implementation begins.
- Implementation includes simulator screenshot evidence and a Jira implementation comment, then stops.
- Screenshot evidence must be real and uploaded to the Jira issue; never fabricate or merely name an attachment that was not uploaded.
- Failed required validation blocks the move to `In Review`.
- Blocking code-review findings block completion.
- Do not fabricate screenshots, logs, test results, Jira proof, or review outcomes.
- Respect repository conventions, required human approvals, branch protection, CI, merge policies, and deployment controls.
- Inspect actual Jira transitions and GitHub repository state instead of assuming names or permissions.

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
4. Use the primary implementation command or one of the explicit follow-up commands above.

Kiro skills can also be exposed as slash commands when they are available as skills in the workspace/global skill directories.

## MCP credentials

Do not commit Jira, Figma, GitHub, or other access tokens to this repository. Keep credentials in the Kiro/MCP configuration mechanism provided by your environment.

## Project customization

This repository is intentionally generic. For a specific iOS codebase, add project-specific conventions to the `steering/` files or create additional skills. Avoid hard-coding company-private names, tokens, URLs, or source code into the reusable Power.

## Recommended workflow

The Power separates discovery, implementation evidence, validation, pull request creation, code review, and completion. Kiro should inspect Jira, Figma, and the existing codebase first, create or reuse the appropriate ticket branch, implement the change, capture and upload simulator evidence to Jira, add the implementation comment, and then stop. Validation, PR creation, code review, and Jira completion are explicit later stages and must respect their gates.
