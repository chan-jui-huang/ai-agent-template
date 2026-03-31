---
name: create-spec
description: Extract a SpecKit `spec.md` from an existing feature or module by reading code, tests, and adjacent docs, then inferring the current product requirement with explicit ambiguity markers.
metadata:
  version: '0.2.0'
---

# Create Spec From Existing Code

## Overview

Use this skill when the goal is to reverse-engineer an existing feature or module into a SpecKit `spec.md`, not to invent a brand-new requirement from scratch.

Treat the codebase as the primary source of truth. User-provided context is optional support, not authority over clear implemented behavior.

## Inputs

- `target`: feature/module path, files, or functional boundary to inspect
- `spec_path`: optional destination `spec.md` path
- `hint`: optional business context or naming hints

## Instructions

1. Read `references/workflow.md` and follow it as the authoritative runbook for this skill. **You must follow it in full.**
2. Use `assets/spec-template.md` as the single source of truth for the generated `spec.md` structure.
3. Keep the final `spec.md` focused on WHAT/WHY in Traditional Chinese (`zh-TW`), not implementation details.
4. If a previous `spec.md` exists, update it into one coherent current specification instead of layering patchwork edits on top.

## Referenced Files

- `assets/spec-template.md`
- `references/workflow.md`

## Changelog

- `0.2.0`: Consolidate the deprecated custom prompt into the workspace skill and move the detailed runbook to `references/workflow.md`.
