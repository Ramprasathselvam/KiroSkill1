---
name: implementation-workflow
description: Orchestrate Jira-driven iOS implementation and, when explicitly requested, the later validation, pull request, review, and completion lifecycle.
---

# Implementation Workflow

Use `Implement <JIRA-KEY>` for implementation-only work. The implementation command must stop after code implementation. Later validation, pull request, review, and completion steps are explicit follow-up commands.

## Primary implementation command

Example:

```text
Implement NCAPP-4521
```

The command performs only:

`Jira status check → branch setup → Figma/discovery → implementation → stop`

It must not automatically continue into validation, Jira `In Review`, pull request creation, code review, or Jira `Done`.

## 1. Start the ticket

- Extract the Jira issue key from the request.
- Fetch the Jira issue and inspect its current status.
- If the issue is `To Do`, transition it to `In Progress` **before creating or modifying implementation code**.
- If it is already `In Progress`, continue.
- If it is in another state, inspect the available Jira transitions and choose the safest valid transition. Do not force an invalid transition.
- If the issue is `Done`, stop and ask before changing anything.

## 2. Branching strategy

Create or use a dedicated branch for ticket implementation.

Branch naming:

- Feature ticket: `feature/<JIRA-KEY>-<short-description>`
- Bug ticket: `bug/<JIRA-KEY>-<short-description>`

Examples:

```text
feature/NCAPP-4521-battery-departure
bug/NCAPP-4522-battery-limit
```

Rules:

- Determine whether the Jira ticket is a feature or bug from its issue type, summary, and metadata.
- Use lowercase for the branch prefix: `feature/` or `bug/`.
- Keep the Jira key exactly as provided by Jira.
- Use a short, readable, kebab-case description.
- Before creating a branch, inspect the repository for an existing branch naming convention and follow it when it is more specific or required by repository policy.
- If a matching ticket branch already exists, reuse it instead of creating a duplicate.
- Base a new branch on the repository's normal development base branch unless the repository explicitly defines another base.
- Do not commit directly to the protected/default branch when a ticket branch is required.

## 3. Discover before editing

- Read Jira title, description, issue type, acceptance criteria, comments, attachments, linked issues, labels, and Figma URLs.
- Inspect the linked Figma design when available.
- Search the existing iOS repository before creating files or architecture.
- Identify existing views/view controllers, view models/state owners, models, API clients, navigation/coordinators, design-system components, assets, localization, and tests.
- Resolve ambiguity before making assumptions.

## 4. Plan

Create a concise implementation plan covering:

- Files/components to modify or add.
- Existing components to reuse.
- API/data changes.
- Navigation and state handling.
- Figma-specific UI requirements.
- Tests that may be appropriate later.

For routine feature work, proceed after discovery and planning. Ask for approval before broad, destructive, shared-infrastructure, or materially ambiguous changes.

## 5. Implement

- Implement the Jira requirements and Figma design in the existing architecture.
- Prefer reuse over duplication.
- Respect the project's Swift, SwiftUI, UIKit, deployment-target, localization, accessibility, networking, and testing conventions.
- Do not make unrelated refactors.
- Keep Jira status at `In Progress` during implementation.
- Keep all implementation changes on the ticket branch.

## 6. Stop after implementation

When the user invoked `Implement <JIRA-KEY>`, stop after the implementation work is complete.

Do not automatically:

- run the full validation gate,
- add a Jira completion/proof comment,
- move Jira to `In Review`,
- create a pull request,
- review a pull request,
- or move Jira to `Done`.

Report what was implemented and the branch used. Do not claim validation passed unless validation was explicitly run.

## Explicit follow-up commands

Use separate commands for later lifecycle stages:

```text
Validate NCAPP-4521
Create PR for NCAPP-4521
Review PR for NCAPP-4521
Complete NCAPP-4521
```

These commands may advance through the corresponding gates only when their required checks pass.

## 7. Validation gate

When the user explicitly requests validation:

1. Build the relevant target/scheme.
2. Run relevant unit tests.
3. Run relevant UI tests when applicable.
4. Verify every Jira acceptance criterion individually.
5. Verify navigation and state transitions.
6. Verify loading, success, empty, disabled, and error states when applicable.
7. Verify localization and accessibility when applicable.
8. Compare against Figma when Figma tooling is available.

If validation fails:

- Do not move Jira to `In Review`.
- Fix the implementation when requested or appropriate.
- Re-run validation.
- Repeat until validation passes or report exactly what is blocked.

Never claim validation passed when a required check failed or was not run.

## 8. Jira proof and In Review gate

Only during the explicit review/PR progression:

- Add a Jira comment containing an evidence-based implementation and validation summary.
- Include build, test, acceptance-criteria, Figma, and known-limitation results only when actually verified.
- Include the pull request URL after the PR exists.
- Only after successful validation, transition Jira from `In Progress` to `In Review` using the valid Jira transition.

Suggested Jira comment:

```markdown
## Implementation Completed

### Implementation
- <summary of changes>

### Validation
- Build: PASS
- Unit Tests: PASS
- UI Tests: PASS / Not applicable
- Acceptance Criteria: PASS
- Figma Validation: PASS / Not applicable

### Notes
- <limitations or relevant notes>

### Pull Request
- <PR URL>
```

Do not attach fabricated screenshots, logs, or test results.

## 9. Pull request

When the user explicitly requests PR creation:

- Confirm the ticket branch exists and contains the intended changes.
- Commit only relevant implementation and test changes.
- Push the ticket branch.
- Create a pull request against the project's normal base branch.
- Use a Jira-linked PR title, for example `NCAPP-4521: <Jira title>`.
- Include Jira, summary, Figma, implementation details, and actual validation results in the PR description.

## 10. Code review gate

When the user explicitly requests review:

- Review the actual GitHub PR diff.
- Check correctness and Jira acceptance criteria.
- Check Swift/SwiftUI/UIKit architecture consistency.
- Check state management, lifecycle, networking/data handling, error handling, accessibility/localization, tests, regressions, security/privacy, and Figma deviations.

If blocking findings exist:

- Keep Jira in `In Review`.
- Fix the findings.
- Re-run the required validation.
- Update the PR.
- Review again.

Do not mark the ticket `Done` while blocking review findings remain.

## 11. Done gate

Only during the explicit completion command:

- Confirm final validation state.
- Confirm the PR meets the configured review requirement.
- Transition Jira from `In Review` to `Done` using the valid Jira transition.
- Respect repository policy requiring human review, merge, CI, or deployment.

## Failure and recovery rules

- Never skip Jira status transitions silently.
- Never modify implementation code before moving a `To Do` ticket to `In Progress`.
- Never create duplicate ticket branches when a matching branch already exists.
- Never move a ticket forward after failed required validation.
- Never create fake proof or claim a test passed without running it.
- Never expose or commit credentials.
- If Jira transition names differ from `To Do`, `In Progress`, `In Review`, and `Done`, inspect the actual available transitions and map them safely.
- If a required Jira, Figma, GitHub, build, or test operation is unavailable, report the exact blocked step instead of pretending it succeeded.
