---
name: jira-to-ios
description: Fetch and interpret a Jira issue before implementing an iOS feature. Use when a Jira issue key or Jira feature request is provided.
---

# Jira to iOS

## Goal

Turn Jira requirements into implementation-ready functional requirements without inventing missing behavior.

## Procedure

1. Identify the Jira issue key.
2. Fetch the issue using the configured Jira MCP integration.
3. Read title, description, acceptance criteria, comments, attachments, linked issues, labels, and URLs.
4. Extract Figma URLs and preserve the exact URLs for the next stage.
5. Separate requirements into functional behavior, UI behavior, data/API behavior, navigation, validation, error/loading/empty states, and non-functional requirements.
6. Resolve contradictions using the latest explicit requirement; flag unresolved ambiguity.
7. Summarize the requirements before implementation.

## Rules

- Do not fabricate acceptance criteria.
- Do not mark a requirement complete without validation.
- Jira credentials must never appear in source files or commits.
- If there is no Figma link, continue using Jira and existing project conventions.
