---
name: github-issue-draft
description: Draft clear, concise, future-proof GitHub issues from raw notes. Use when the user asks to create, write, format, or turn notes into a GitHub issue, especially when they want a Markdown issue file or a structured issue body.
---

# GitHub Issue Draft

Transform the user's raw notes into one Markdown issue. Use English for the title and body. Keep the tone professional, neutral, technical, and concise.

## Required Structure

Use these sections in this order unless the user explicitly asks for a different format:

```md
## Title

## Summary

## Scope / What to do

## Current Problem / Root Cause (if applicable)

## Examples

## Impact

## Proposed Solution / Recommended Fix

## Files / Locations Involved

## Acceptance Criteria / How to know it's fixed

## Related Issues / Context

## Status (only if relevant)
```

## Writing Rules

- Make the title one clear, searchable line starting with a verb or problem.
- Keep the summary to 1-3 sentences.
- Make scope bullets concrete and testable.
- Include 2-5 concrete examples when available.
- Use relative paths for files and folders.
- Make acceptance criteria observable.
- Default status to `New` only when status is useful.
- Do not add fluff or unrelated sections.
