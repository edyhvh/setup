---
name: feature-bootstrap
description: Bootstrap a new product feature with a clear implementation plan, file map, delivery slices, and acceptance criteria.
---

# Feature Bootstrap

Use this skill when starting a new feature from a rough idea, issue text, or product note.

## Inputs to Collect

- Problem statement and user outcome.
- Existing constraints (runtime, stack, platform, deadlines).
- Existing files or modules likely to be touched.
- Non-goals and explicit exclusions.

## Output Contract

Produce these sections in order:

1. Scope Summary
2. Assumptions and Risks
3. File and Module Plan
4. Incremental Delivery Slices
5. Test Plan
6. Rollout and Fallback

## Rules

- Keep scope minimal and shippable.
- Prefer additive, low-risk changes before refactors.
- Define acceptance criteria as observable outcomes.
- Avoid speculative architecture unless required.
- Flag unknowns explicitly instead of inventing details.

## Acceptance Criteria Pattern

- Given <context>, when <action>, then <expected behavior>.
- Include at least one negative-path criterion.
- Include one criterion for telemetry/logging if relevant.
