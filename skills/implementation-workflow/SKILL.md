---
name: implementation-workflow
description: Orchestrate the complete Jira-to-iOS delivery lifecycle. Use when implementing a Jira issue and the workflow must update Jira status, validate the implementation, add proof, create a pull request, and complete code review.
---

# Implementation Workflow

Follow this lifecycle for Jira-driven iOS feature work. Treat each transition as a gate; never advance Jira because code was merely generated.

## Lifecycle

`To Do` → `In Progress` → implementation → validation → `In Review` → pull request → code review → `Done`

## 1. Start the ticket

- Extract the Jira issue key from the request.
- Fetch the Jira issue and inspect its current status.
- If the issue is `To Do`, transition it to `In Progress` before implementation.
- If it is already `In Progress`, continue.
- If it is in another state, inspect the available transitions and choose the safest valid transition; do not force an invalid transition.
- If the issue is `Done`, stop and ask before changing anything.

## 2. Discover before editing

- Read Jira title, description, acceptance criteria, comments, attachments, linked issues, labels, and Figma URLs.
- Inspect the linked Figma design when available.
- Search the existing iOS repository before creating files or architecture.
- Identify existing views/view controllers, view models/state owners, models, API clients, navigation/coordinators, design-system components, assets, localization, and tests.
- Resolve ambiguity before making assumptions.

## 3. Plan

Create a concise implementation plan covering:

- Files/components to modify or add.
- Existing components to reuse.
- API/data changes.
- Navigation and state handling.
- Tests and validation strategy.
- Figma-specific UI requirements.

For routine feature work, proceed after discovery and planning. Ask for approval before broad, destructive, shared-infrastructure, or ambiguous changes.

## 4. Implement

- Implement the Jira requirements and Figma design in the existing architecture.
- Prefer reuse over duplication.
- Respect the project's Swift, SwiftUI, UIKit, deployment-target, localization, accessibility, networking, and testing conventions.
- Do not make unrelated refactors.
- Keep Jira status at `In Progress` during implementation.

## 5. Validation gate

Before moving Jira to `In Review`, all applicable validation must pass:

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
- Fix the implementation.
- Re-run validation.
- Repeat until validation passes or the user is told exactly what is blocked.

Never claim validation passed when a required check failed or was not run.

## 6. Jira proof and In Review gate

After validation passes:

- Add a Jira comment containing an implementation and validation summary.
- Include the Jira issue key, implementation summary, build result, test result, acceptance-criteria result, Figma result, and any known limitations.
- Include the pull request URL after the PR exists; if the PR must be created after the status transition, add/update the comment once the URL is available.
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

Do not attach fabricated screenshots, logs, or test results. Proof must come from actual work performed.

## 7. Pull request

- Create or switch to a feature branch using the project's existing branch naming convention.
- Prefer a Jira-linked branch name such as `feature/NCAPP-4521-<short-description>` when no project convention exists.
- Commit only relevant implementation and test changes.
- Create a pull request against the project's normal base branch.
- Use a Jira-linked PR title, for example `NCAPP-4521: <Jira title>`.
- Include Jira, summary, Figma, implementation details, and validation results in the PR description.

## 8. Code review gate

Review the pull request after it is created.

Review for:

- Correctness and Jira acceptance criteria.
- Swift/SwiftUI/UIKit architecture consistency.
- State management and lifecycle issues.
- Networking/data handling.
- Error handling.
- Accessibility/localization.
- Tests and test coverage.
- Unnecessary changes or regressions.
- Security/privacy issues.
- Figma/design deviations.

If blocking findings exist:

- Keep Jira in `In Review`.
- Fix the findings.
- Re-run the required validation.
- Update the PR.
- Review again.

Do not mark the ticket `Done` while blocking review findings remain.

## 9. Done gate

Only when the PR has passed the configured review gate:

- Confirm the final validation state.
- Confirm the PR is approved or otherwise meets the project's configured review requirement.
- Transition Jira from `In Review` to `Done` using the valid Jira transition.
- Report the final Jira status and PR URL.

If repository policy requires a human reviewer, merge, or deployment before `Done`, do not bypass that policy. Stop at the required gate and report what remains.

## Failure and recovery rules

- Never skip Jira status transitions silently.
- Never move a ticket forward after a failed required validation.
- Never create fake proof or claim a test passed without running it.
- Never expose or commit credentials.
- If Jira transition names differ from `To Do`, `In Progress`, `In Review`, and `Done`, inspect the actual available transitions and map them to the lifecycle.
- If a required Jira, Figma, GitHub, build, or test operation is unavailable, report the exact blocked step instead of pretending it succeeded.
