---
name: create-plan
description: Extract a SpecKit-style `plan.md` from an existing feature or module by reading code, tests, and the corresponding `spec.md`, then ordering each user story by implementation dependency and documenting the story-level technical plan in terms of input, output, and transformation logic.
metadata:
  version: '0.2.0'
---

# Create Plan From Existing Code

## Overview

Use this skill when the goal is to reconstruct the effective implementation plan of an existing feature or module rather than to design a brand-new feature.

The codebase is the primary source of truth. The corresponding `spec.md` is required and acts as the user-story frame. Optional user hints are secondary context only.

## Inputs

- `target`: the implementation boundary to inspect
- `spec.md`: the corresponding feature specification
- `plan_path`: optional destination for the generated or updated `plan.md`
- `plan_hint`: optional planning intuition or emphasis from the user

## Instructions

1. Read `references/workflow.md` and follow it as the authoritative runbook for this skill. **You must follow it in full.**
2. Use `assets/plan-template.md` as the single source of truth for the generated `plan.md` structure.
3. Keep the final `plan.md` focused on HOW in Traditional Chinese (`zh-TW`), not product requirement details.
4. Base story order on implementation dependency rather than the original `spec.md` order when the code shows a different dependency chain.
5. If an older `plan.md` exists, update it into one coherent current plan instead of layering patchwork edits on top.

## Referenced Files

- `assets/plan-template.md`
- `references/workflow.md`

## Changelog

- `0.2.0`: Consolidate the deprecated custom prompt into the workspace skill and move the detailed runbook to `references/workflow.md`.
