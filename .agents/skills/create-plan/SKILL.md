---
name: create-plan
description: Extract a SpecKit-style `plan.md` from an existing feature or module by reading code, tests, and the corresponding `spec.md`, then ordering each user story by implementation dependency and documenting the story-level technical plan in terms of input, output, and transformation logic.
compatibility: Requires access to the workspace files for the target code and its corresponding `spec.md`. The generated plan should follow the shared template in `assets/plan-template.md`.
metadata:
  version: "0.1.0"
  scope: "workspace"
---

# Create Plan From Existing Code

## Overview

Use this skill when the goal is to reconstruct the effective implementation plan of an existing feature or module rather than to design a brand-new feature.

This workflow assumes:

- code is the primary source of truth,
- `spec.md` is required and acts as the user-story frame,
- optional user hints about the "intended plan" are secondary,
- the resulting `plan.md` must show the dependency-ordered technical plan for every user story currently supported by the codebase.

## Required Inputs

The caller must provide all of the following:

- the target implementation boundary (`target`),
- the corresponding `spec.md`,
- and either an explicit `plan.md` destination or enough context to infer one safely.

If the spec or code boundary is missing, stop and ask for the missing input instead of guessing.

## Workflow

1. Identify the plan boundary
   - Start from the user-provided target files or directories.
   - Confirm which `spec.md` applies to that implementation.
   - Keep the scope narrow enough that one coherent `plan.md` can describe it.

2. Gather the highest-signal evidence
   - Read the target `spec.md` first to identify user stories, terminology, and story boundaries.
   - Read an existing `plan.md` next if one already exists.
   - Inspect controllers, services, domain logic, data access, jobs, contracts, and tests that implement each story.
   - Read adjacent docs only when they clarify technical intent, dependency order, or shared constraints.

3. Recover the real story graph
   - Enumerate every user story from `spec.md`.
   - Map each story to the code paths that currently implement it.
   - Identify prerequisite stories, shared setup stories, and downstream stories.
   - Reorder the stories by implementation dependency rather than by the original document order when the code clearly requires it.

4. Extract the story-level implementation plan
   - For each user story, identify the externally meaningful inputs.
   - Identify the concrete outputs that the implementation produces.
   - Write a prose description of how the program transforms the input into the output.
   - Summarize the participating modules, persistence surfaces, side effects, authorization, validation, and failure boundaries only at the level needed to explain the flow.

5. Capture shared implementation decisions
   - Summarize the technical context that multiple stories rely on.
   - Summarize the project structure that contains the relevant docs and implementation surfaces for this feature.
   - Include shared data relationships, integration boundaries, validation patterns, concurrency/consistency rules, and verification strategy when they are evidenced by the code.
   - Keep this section architectural and high-level rather than file-by-file.

6. Reconcile code with `spec.md`
   - Treat the code as current truth.
   - For every meaningful code/spec mismatch, record the conflict explicitly.
   - If the code only partially implements a story, say so directly instead of inventing missing steps.

7. Draft or update the plan
   - Write a full `plan.md` using `assets/plan-template.md`.
   - If an older plan exists, preserve accurate material, update stale sections, and reorganize only where needed to match the reconstructed dependency order.
   - Ensure every story plan includes `Input`, `Output`, and `How Input Becomes Output`.

8. Validate before finishing
   - Confirm the plan stays high-level and implementation-oriented.
   - Confirm each story is supported by real code evidence.
   - Confirm story order reflects dependency order.
   - Confirm every code/spec conflict is captured explicitly.
   - Confirm no template placeholders remain.

## Output Contract

The generated `plan.md` should follow this shape:

- `# Implementation Plan: ...`
- metadata header with date, spec path, and target input summary
- `## Scope`
- `## Source Alignment`
- `## Technical Context`
- `## Project Structure`
- `## Story Dependency Order`
- `## User Story Plans`
- `## Code vs Spec Conflicts`
- `## Verification Focus`

Each user story plan must include:

- a dependency note,
- `Input`,
- `Output`,
- `How Input Becomes Output`,
- implementation surfaces,
- data or integration notes when applicable.

Write the plan primarily in Traditional Chinese (`zh-TW`).

## Output Template

Use the shared template at:

- `assets/plan-template.md`

Rules:

- Treat `assets/plan-template.md` as the single source of truth for the generated `plan.md` structure.
- Replace every placeholder with extracted content from the codebase, tests, and `spec.md`.
- Do not leave placeholder text, comments, or instructional text in the final file.
- Use real repository paths in `## Project Structure` and keep only the directories or modules that materially support the covered feature.

## Decision Rules

- Prefer implemented control flow over presumed architecture.
- Prefer observed dependency chains over `spec.md` ordering when they conflict.
- Prefer shared business/domain logic over transport-layer wrappers.
- Prefer concise architectural summaries over exhaustive implementation inventories.
- Prefer explicit conflict records over silent normalization between code and `spec.md`.
- If the provided scope contains multiple unrelated capabilities, narrow to the smallest coherent feature boundary that still matches the supplied `spec.md`.

## When To Stop And Ask

Ask the user only if one of these is true:

- the supplied `spec.md` and target code boundary do not appear to describe the same feature,
- the scope contains multiple unrelated features and no safe default boundary exists,
- the destination path for `plan.md` is genuinely ambiguous,
- the codebase lacks enough evidence to map any `spec.md` story to implementation.

Otherwise, make the narrowest reasonable assumption and continue.

## Example Prompts

```text
Use the create-plan skill to reconstruct `docs/specs/002-admin-user/plan.md` from `docs/specs/002-admin-user/spec.md` and the implementation under `internal/http/controller/admin_user`, `internal/pkg/user`, and related tests.
```

```text
Use the create-plan skill on `backend/src/modules/billing` with `docs/specs/billing/spec.md`. Order the story plans by code dependency, not by the original story order, and record any code/spec mismatches explicitly.
```

```text
Use the create-plan skill to update `specs/001-telegram-notification-jobs/plan.md` from the current code and `specs/001-telegram-notification-jobs/spec.md`. Treat this note as optional context only: "I think scheduling should appear after chat binding."
```

## Expected Agent Response Pattern

- Confirm the target boundary and the matching `spec.md`.
- Inspect code, tests, and adjacent docs before writing.
- Create or update the `plan.md`.
- Return the completion response required by the invoking prompt, client, or environment.

## Acceptance Checks

- [ ] The skill produces a full `plan.md`, not scattered notes.
- [ ] The resulting plan is driven primarily by code evidence.
- [ ] `spec.md` user stories are reordered by implementation dependency when necessary.
- [ ] Every story plan includes `Input`, `Output`, and `How Input Becomes Output`.
- [ ] `## Project Structure` reflects the real documentation and source-code layout for the feature boundary.
- [ ] Code/spec conflicts are captured explicitly.
- [ ] The final writing stays high-level and implementation-oriented.
- [ ] No placeholder text from templates remains.

## Changelog

- `0.1.0`: Initial workspace skill for extracting dependency-ordered implementation plans from existing code and `spec.md`.
