---
name: implementation-workflow
description: Orchestrate Jira-driven iOS implementation and, when explicitly requested, the later validation, pull request, review, and completion lifecycle.
---

# Implementation Workflow

Use `Implement <JIRA-KEY>` for implementation-only work. The implementation command must stop after implementation evidence is recorded. Later validation, pull request, review, and completion steps are explicit follow-up commands.

## Permission gates

The workflow uses explicit user approval for impactful lifecycle actions. A permission gate is an interactive stop point: ask a clear Yes/No question in chat and **wait for the user's actual response before taking the gated action**.

Rules:

- Never infer approval from the original command, silence, context, or a previous approval for a different action.
- Do not perform the gated action until the user explicitly answers `Yes` (or an unambiguous equivalent such as `Y`, `Proceed`, or `Continue`).
- Treat `No`, `N`, `Cancel`, or `Stop` as rejection and stop that workflow stage.
- After a rejection, do not retry the same action automatically and do not continue to later lifecycle stages.
- A single approval applies only to the specific action described in that prompt.
- Keep the prompt specific about what will happen and the relevant Jira/branch/PR target.
- Do not present an approval prompt and then continue in the same turn without receiving the user's response.

Suggested prompt format:

```text
Jira issue KAN-1 is currently To Do.
I need to move it to In Progress before modifying implementation code.
Proceed? [Yes/No]
```

## Primary implementation command

Example:

```text
Implement NCAPP-4521
```

The command performs only:

`Jira status check → approval for required status transition → branch setup → Figma/discovery → implementation → simulator screenshot evidence → Jira implementation comment → stop`

It must not automatically continue into the full validation gate, Jira `In Review`, pull request creation, code review, or Jira `Done`.

## 1. Start the ticket

- Extract the Jira issue key from the request.
- Fetch the Jira issue and inspect its current status.
- If the issue is `To Do`, **ask for permission before transitioning it to `In Progress`**.
- Example prompt:

```text
Jira issue <JIRA-KEY> is currently To Do.
I need to move it to In Progress before modifying implementation code.
Proceed? [Yes/No]
```

- If the user says `Yes`, perform the valid Jira transition and continue.
- If the user says `No`, stop immediately. Leave the Jira status unchanged and make no implementation code changes.
- If it is already `In Progress`, continue without asking for the already-satisfied transition.
- If it is in another state, inspect the available Jira transitions and choose the safest valid transition. If that transition changes workflow state materially, ask for permission before performing it.
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

## 6. Simulator screenshot and Jira evidence

After implementation is complete, capture visual evidence of the implemented screen **without turning this into the full validation workflow**.

### Screenshot rules

- Use the iOS Simulator when a simulator is available.
- Launch/run the relevant app and navigate to the implemented screen when this can be done reliably.
- Capture a PNG screenshot using the simulator tooling, for example:

```bash
xcrun simctl io booted screenshot /tmp/NCAPP-4521.png
```

- If multiple screens are central to the ticket, capture the minimum useful set of screenshots rather than every screen.
- Do not claim a screenshot was captured if the simulator is unavailable or the screen could not be reached.
- Do not fabricate screenshots.

### Jira attachment and comment

- Upload the captured PNG file to the Jira issue using the available Jira attachment-upload capability.
- The Jira attachment must be uploaded **before** adding the implementation comment so the evidence is already present on the issue.
- Add an evidence-based Jira implementation comment after the screenshot upload.
- The comment should reference the uploaded screenshot filename(s) and summarize what was implemented.
- Do not claim build/test/acceptance/Figma validation passed unless those checks were explicitly run.

Suggested implementation comment:

```markdown
## Implementation Completed

### Implementation
- <summary of changes>

### Simulator Evidence
- <screenshot filename uploaded to Jira>

### Validation
- Not run as part of implementation-only workflow.

### Branch
- `<feature/JIRA-KEY-short-description>` or `<bug/JIRA-KEY-short-description>`
```

If Jira attachment upload is unavailable, keep the issue `In Progress`, report that the screenshot could not be attached, and do not pretend the upload succeeded.

## 7. Stop after implementation

When the user invoked `Implement <JIRA-KEY>`, stop after implementation, screenshot evidence, and Jira implementation evidence are complete.

Do not automatically:

- run the full validation gate,
- move Jira to `In Review`,
- create a pull request,
- review a pull request,
- or move Jira to `Done`.

Report what was implemented, the branch used, and whether the simulator screenshot was successfully uploaded to Jira.

## Explicit follow-up commands

Use separate commands for later lifecycle stages:

```text
Validate NCAPP-4521
Create PR for NCAPP-4521
Review PR for NCAPP-4521
Complete NCAPP-4521
```

These commands may advance through the corresponding gates only when their required checks pass **and any required permission gate is approved**.

## 8. Validation gate

When the user explicitly requests validation:

1. Build the relevant target/scheme.
2. Run relevant unit tests.
3. Run relevant UI tests when applicable.
4. Verify every Jira acceptance criterion individually.
5. Verify navigation and state transitions.
6. Verify loading, success, empty, disabled, and error states when applicable.
7. Verify localization and accessibility when applicable.
8. Compare against Figma when Figma tooling is available.
9. Run configured lint/static-analysis checks when present.
10. Inspect test/lint output for failures and warnings.

If validation fails:

- Do not move Jira to `In Review`.
- Fix the implementation when requested or appropriate.
- Re-run validation.
- Repeat until validation passes or report exactly what is blocked.

Never claim validation passed when a required check failed or was not run.

## 9. Jira proof and In Review gate

Only during the explicit review/PR progression:

- Add or update a Jira comment containing an evidence-based implementation and validation summary.
- Include build, test, acceptance-criteria, Figma, lint, and known-limitation results only when actually verified.
- Include the pull request URL after the PR exists.
- Only after successful validation, transition Jira from `In Progress` to `In Review` using the valid Jira transition.
- If transitioning to `In Review` is a separate user-impacting action in the requested workflow, ask for approval immediately before the transition and wait for the response.

## 10. Pull request

When the user explicitly requests PR creation:

### Mandatory pre-PR checks

Before creating the pull request, the workflow **must** check the implementation for test failures and lint warnings/errors.

1. Confirm the ticket branch contains only the intended changes.
2. Identify the project's actual build, test, and lint commands from repository configuration and conventions.
3. Build the relevant iOS target/scheme when required to establish that the code compiles.
4. Run the relevant unit tests and UI tests when applicable.
5. Run the configured lint/static-analysis checks (for example SwiftLint or the project's equivalent) when present.
6. Inspect the command output for both failures and warnings.
7. Treat **any test failure, lint error, or lint warning as a PR blocker** unless the repository explicitly documents that the warning is intentionally allowed/ignored.
8. Do not create or push a pull request while any blocking test failure or lint warning/error remains.
9. Report the exact failing test, lint warning/error, command, and affected file/line when available.
10. Fix the issue and re-run the affected checks before attempting PR creation again.

A successful build alone is not sufficient for PR creation. The required tests and lint checks must also pass without blocking warnings/errors.

### PR creation permission gate

Only after all mandatory pre-PR checks pass, **ask the user for explicit permission before pushing the ticket branch or creating the PR**.

Example:

```text
Validation passed for <JIRA-KEY>.
I am ready to push <branch-name> and create the pull request against <base-branch>.
Proceed? [Yes/No]
```

- If `Yes`, commit only relevant changes as needed, push the ticket branch, and create the PR.
- If `No`, stop PR creation and leave the branch/working state unchanged except for any already-existing local changes.
- Do not create the PR first and ask permission afterward.
- Do not treat a prior approval for implementation or validation as approval for PR creation.

### PR creation details

After explicit PR approval:

- Commit only relevant implementation and test changes.
- Push the ticket branch.
- Create a pull request against the project's normal base branch.
- Use a Jira-linked PR title, for example `NCAPP-4521: <Jira title>`.
- Include Jira, summary, Figma, implementation details, screenshot evidence, and actual validation results in the PR description.

If a required test or lint check cannot be run because the required tooling/environment is unavailable, **do not create the PR**. Report the blocked check and why it could not be verified.

## 11. Code review gate

When the user explicitly requests review:

- Review the actual GitHub PR diff.
- Check correctness and Jira acceptance criteria.
- Check Swift/SwiftUI/UIKit architecture consistency.
- Check state management, lifecycle, networking/data handling, error handling, accessibility/localization, tests, regressions, security/privacy, and Figma deviations.

### Review permission gate

Before performing an external review action such as posting a GitHub review or review comments, ask the user for explicit permission.

Example:

```text
PR #123 for <JIRA-KEY> is ready for review.
Do you want me to submit the review/comments to GitHub? [Yes/No]
```

- If `Yes`, submit the appropriate review action.
- If `No`, stop before posting the review; report the findings locally without submitting them.
- Do not interpret the user's request to inspect/review code as permission to publish a GitHub review when this gate applies.

If blocking findings exist:

- Keep Jira in `In Review`.
- Fix the findings when explicitly requested/approved.
- Re-run the required validation.
- Update the PR.
- Review again.

Do not mark the ticket `Done` while blocking review findings remain.

## 12. Done gate

Only during the explicit completion command:

- Confirm final validation state.
- Confirm the PR meets the configured review requirement.
- **Ask for explicit permission immediately before moving Jira from `In Review` to `Done`.**

Example:

```text
Validation and review requirements for <JIRA-KEY> are complete.
Do you want me to move Jira from In Review to Done? [Yes/No]
```

- If `Yes`, perform the valid Jira transition.
- If `No`, leave the issue in its current state and stop.
- Respect repository policy requiring human review, merge, CI, or deployment.

## Failure and recovery rules

- Never skip Jira status transitions silently.
- Never modify implementation code before moving a `To Do` ticket to `In Progress`.
- Never move `To Do` to `In Progress` without the required explicit user approval.
- Never create duplicate ticket branches when a matching branch already exists.
- Never move a ticket forward after failed required validation.
- Never create fake screenshots or claim a screenshot was uploaded without successfully performing the upload.
- Never create fake proof or claim a test passed without running it.
- Never create a PR when a required test has failed or a blocking lint warning/error is present.
- Never create a PR without the required explicit user approval at the PR creation gate.
- Never publish a GitHub review without the required explicit user approval at the review gate.
- Never move Jira to `Done` without the required explicit user approval at the completion gate.
- Never create a PR when required build/test/lint tooling is unavailable and the check cannot be verified.
- Never expose or commit credentials.
- If Jira transition names differ from `To Do`, `In Progress`, `In Review`, and `Done`, inspect the actual available transitions and map them safely.
- If a required Jira, Figma, GitHub, simulator, build, test, or lint operation is unavailable, report the exact blocked step instead of pretending it succeeded.
