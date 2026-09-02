# Testing Conventions

Follow the existing project's testing framework and naming conventions.

For each feature:

- Add or update unit tests for new business logic.
- Test important state transitions and validation rules.
- Reuse existing mocks/stubs/fixtures.
- Add UI tests only where the project already uses them or where the Jira requirement needs end-to-end coverage.
- Never delete or weaken existing tests simply to make a build pass.
