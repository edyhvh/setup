---
name: paper-to-nativewind
description: Migrate React Native Paper UI code to NativeWind utility classes while preserving behavior, theming intent, and accessibility.
license: MIT
---

# Paper to NativeWind

Use this skill when refactoring existing React Native Paper screens/components into a NativeWind-first implementation.

## Goals

- Keep feature behavior unchanged.
- Keep accessibility semantics unchanged or better.
- Translate style intent into utility classes with clear composition.
- Avoid introducing visual regressions in spacing, typography, and states.

## Recommended Workflow

1. Identify all Paper components in scope (for example: `Button`, `Card`, `TextInput`, `Chip`, `Snackbar`, `Dialog`).
2. Map each component to a NativeWind-native layout and primitive set.
3. Preserve component props and interaction contracts.
4. Replace theme-based style objects with utility class tokens.
5. Validate disabled/loading/error/focus states.
6. Verify parity on small and large screen sizes.

## Conversion Rules

- Prefer incremental migration per component or screen, not large rewrites.
- Keep state logic and business logic untouched unless required for parity.
- Do not silently drop variants (outlined, elevated, tonal, etc.); map each one.
- Replace one-off inline styles with reusable className patterns when repeated.
- Keep text contrast and tap targets compliant.
- If exact parity is impossible, document tradeoffs explicitly.

## Output Expectations

- Updated component code using NativeWind className conventions.
- Brief migration notes listing mapped components and any accepted differences.
- Clear TODO markers for unresolved parity gaps.

## Validation Checklist

- Interaction parity: press, focus, disabled, loading.
- Visual parity: spacing, border radius, elevation/shadow, typography.
- Accessibility: labels, roles, hints, hit slop where needed.
- Responsiveness: no clipping or overlap on narrow devices.
