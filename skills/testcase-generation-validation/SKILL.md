---
name: testcase-generation-validation
description: Generate Jira-driven iOS test cases and validate implementation against acceptance criteria, automated tests, UI behavior, and lint requirements.
---

# Test Case Generation and Validation

Use this skill when the user explicitly requests test-case generation, test implementation, or validation for a Jira ticket.

## Commands

Examples:

```text
Generate test cases for NCAPP-4521
Validate NCAPP-4521
Generate and validate test cases for NCAPP-4521
```

## 1. Understand the ticket

- Fetch the Jira issue.
- Read the title, description, acceptance criteria, issue type, comments, attachments, linked issues, and relevant Figma links.
- Inspect the implemented code and existing tests before generating new cases.
- Identify the affected screens, view models, models, networking, navigation, state transitions, and reusable components.

## 2. Generate test cases

Generate test cases from Jira acceptance criteria and actual implementation behavior.

Cover, when applicable:

- Happy path.
- Boundary values and limits.
- Empty/default states.
- Loading state.
- Success state.
- Error/failure state.
- Disabled controls.
- User interaction and navigation.
- State restoration and repeated interactions.
- API success/failure and malformed data handling.
- Localization and accessibility.
- Device/orientation behavior when relevant.
- Regression scenarios for existing behavior.

Each test case should contain:

- Test case ID.
- Scenario/title.
- Preconditions.
- Test steps.
- Expected result.
- Automation type: Unit, UI, integration, or manual.
- Related Jira acceptance criterion when identifiable.

Prefer deterministic and independently executable test cases.

## 3. Test implementation

When the user asks to implement the generated test cases:

- Reuse the repository's existing XCTest/XCUITest architecture and helpers.
- Follow existing naming, setup, teardown, mocking, dependency-injection, and test-data conventions.
- Add only tests relevant to the Jira change.
- Do not weaken assertions merely to make tests pass.
- Do not change production behavior solely to hide a failing test.

## 4. Validation

When validation is requested:

1. Build the relevant iOS target/scheme.
2. Run the relevant unit tests.
3. Run relevant UI tests when applicable.
4. Verify Jira acceptance criteria individually against the implementation.
5. Verify navigation and state transitions.
6. Verify relevant loading, success, empty, disabled, and error states.
7. Verify localization and accessibility when applicable.
8. Run the repository's configured lint tool, such as SwiftLint, when available.
9. Check the lint output for warnings and errors.

Do not claim a check passed unless it was actually executed successfully.

## 5. Validation gate for PR creation

`Create PR for <JIRA-KEY>` must use this skill's validation rules before creating the PR.

PR creation is blocked when:

- A required build fails.
- A relevant test fails.
- A relevant UI test fails.
- A required acceptance criterion fails validation.
- Lint reports a warning or error, unless the repository explicitly classifies that warning as allowed.
- Required validation tooling cannot be executed.

When blocked:

- Do not push/create the PR as a completed PR workflow.
- Report the exact failing test, acceptance criterion, lint warning/error, or unavailable tool.
- Keep the ticket in its current appropriate state.
- Fix the issue when explicitly requested, then re-run the failed validation.

## 6. Validation evidence

Record concise evidence for each check:

```markdown
### Test Validation
- Build: PASS/FAIL
- Unit tests: PASS/FAIL — <result>
- UI tests: PASS/FAIL/NOT APPLICABLE — <result>
- Acceptance criteria: PASS/FAIL — <summary>
- Lint: PASS/FAIL — <warnings/errors>

### Blockers
- <exact blocker, if any>
```

Never fabricate test results, lint results, screenshots, or validation evidence.

## 7. Scope boundaries

- Test-case generation does not automatically modify production code.
- Validation does not automatically create a PR unless the user explicitly requests PR creation.
- PR creation must not bypass this validation gate.
- Review and Jira completion remain separate explicit lifecycle commands.
