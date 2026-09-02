---
name: code-review
description: Review the implementation pull request created from a Jira-driven iOS feature. Use after implementation and validation, and before moving the Jira issue to Done.
---

# Code Review

Review the feature PR as a separate quality gate. Do not assume that a successful build means the code is review-ready.

## Review order

1. Read the Jira issue and acceptance criteria.
2. Inspect the pull request title, description, changed files, and diff.
3. Compare the implementation against the linked Figma design when available.
4. Check architecture and project conventions.
5. Check tests and validation evidence.
6. Classify findings.

## Review checklist

### Functional correctness

- Does the implementation satisfy every Jira acceptance criterion?
- Are edge cases handled?
- Are loading, success, empty, disabled, and error states correct where applicable?
- Is navigation correct?

### iOS architecture

- Does the change follow the existing Swift/SwiftUI/UIKit architecture?
- Is state owned by the correct object?
- Is business logic kept out of views when the project architecture separates it?
- Are existing reusable components used where appropriate?
- Are there unnecessary abstractions or unrelated refactors?

### Data and networking

- Are API models and mapping consistent with the project?
- Is error handling appropriate?
- Are loading and failure states safe?
- Are credentials, tokens, or sensitive data protected?

### UI and design

- Does the UI match Figma?
- Are spacing, typography, colors, corner radius, icons, and states correct?
- Are existing design-system components reused?
- Is Dynamic Type/accessibility handled where applicable?
- Are localized strings used instead of hard-coded user-facing text?

### Testing

- Are relevant unit tests present or updated?
- Are important state transitions covered?
- Are UI tests appropriate for the feature?
- Do the reported test results correspond to tests actually run?

## Findings

Classify findings as:

- **Blocking** — must be fixed before completion.
- **Non-blocking** — improvement that does not prevent completion.
- **Informational** — observation only.

A blocking finding means the Jira issue must remain `In Review`.

## Review completion

A review passes only when there are no blocking findings and the required validation remains successful.

Do not move Jira to `Done` merely because the PR exists or because Kiro generated a review. If the repository requires human approval, merge, CI, or deployment, respect that policy.

## Review report

Use this format:

```markdown
## Code Review

### Result
PASS / CHANGES REQUIRED

### Blocking Findings
- <finding or None>

### Non-blocking Findings
- <finding or None>

### Validation
- Build: PASS / FAIL / NOT RUN
- Unit Tests: PASS / FAIL / NOT RUN
- Acceptance Criteria: PASS / FAIL
- Figma: PASS / FAIL / NOT APPLICABLE

### Recommendation
<approve / request changes / wait for required human approval>
```
