---
name: ar-integration
description: Integrate augmented reality features into an existing app with clear boundaries, performance guardrails, and fallback behavior.
---

# AR Integration

Use this skill when introducing AR capabilities into a product that already has core app flows.

## Primary Objectives

- Keep non-AR app paths stable.
- Isolate AR dependencies behind feature flags.
- Define device capability checks and graceful fallbacks.
- Keep rendering and tracking performance within target budgets.

## Planning Checklist

- Required AR features (viewer, anchors, gestures, overlays, exports).
- Platform matrix and minimum OS/device capabilities.
- Runtime permission model and failure modes.
- Telemetry events for capability, entry, errors, and completion.
- QA scenarios for supported and unsupported devices.

## Implementation Rules

- Keep AR layer modular and optional.
- Avoid hard-coupling business logic to AR SDK internals.
- Guard every AR entry path with capability checks.
- Provide a non-AR equivalent flow when possible.
- Document package/version constraints and native setup steps.

## Exit Criteria

- AR entry is blocked cleanly on unsupported devices.
- Core app journey still works without AR enabled.
- Performance is acceptable on the minimum supported device.
- Error states are user-friendly and observable in telemetry.
