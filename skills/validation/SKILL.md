---
name: validation
description: Validate an implemented iOS Jira feature with builds, tests, acceptance criteria, and available Figma comparison.
---

# Validation

## Required checks

1. Build the relevant Xcode target/scheme.
2. Run relevant unit tests.
3. Run UI tests when they exist and are applicable.
4. Re-check each Jira acceptance criterion individually.
5. Verify navigation and state transitions.
6. Verify loading, success, empty, disabled, and error states where applicable.
7. Check localization and accessibility for changed UI.
8. Compare implementation with the relevant Figma frame when visual inspection is available.

## Report

Return:

- Build: pass/fail
- Tests: pass/fail and relevant test names
- Jira criteria: checked item by item
- Figma: verified/unverified and notable differences
- Remaining issues: explicit list

Never report success when a required build or test fails.
