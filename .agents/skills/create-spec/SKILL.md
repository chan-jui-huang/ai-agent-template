---
name: create-spec
description: Extract a SpecKit `spec.md` from an existing feature or module by reading code, tests, and adjacent docs, then inferring the current product requirement with explicit ambiguity markers.
user-invocable: true
metadata:
  version: '0.1.0'
  description: 'Reusable workflow for reverse-engineering an existing implementation into a SpecKit feature specification.'
  scope: workspace
  applyTo:
    - 'docs/specs/**'
    - 'specs/**'
  inputs:
    - name: target
      type: string
      required: true
      description: 'Feature/module path, files, or functional boundary to inspect.'
    - name: spec_path
      type: string
      required: false
      description: 'Destination `spec.md` path. If omitted, infer a repository-consistent path.'
    - name: hint
      type: string
      required: false
      description: 'Optional business context or naming hints supplied by the user.'
  outputs:
    - name: spec_file
      type: file
      description: 'Generated or updated `spec.md` path.'
    - name: feature_summary
      type: string
      description: 'Short summary of the extracted feature boundary and key inferred intent.'
---

# Create Spec From Existing Code

## Overview

Use this skill when the goal is not to invent a new feature spec, but to extract the current behavior of an existing feature or module into a SpecKit `spec.md`.

This workflow assumes the codebase is the primary source of truth. User-provided context is optional and should be treated as supportive context rather than unquestioned truth.

## Workflow

1. Identify the feature boundary
   - Start from the user-provided `target`.
   - Find the narrowest coherent scope that should be described together in a single spec.
   - If the scope is unclear or too broad, narrow it before drafting the spec.

2. Gather the highest-signal evidence
   - Read existing specs, plans, and supporting documents for the same feature if they exist.
   - Inspect the parts of the system that best reveal how the feature behaves in practice.
   - Inspect the logic, rules, and conditions that shape outcomes and constraints.
   - Inspect tests for confirmed behavior, failure paths, and edge cases.
   - Inspect reference material only when it helps recover domain vocabulary or entity meaning.

3. Extract observable behavior
   - Identify the relevant actors or contexts when they are visible in the implementation.
   - Identify the feature's main behaviors and the outcomes they produce.
   - List important success and failure paths.
   - Identify constraints, restrictions, fallback behavior, and recovery behavior.
   - Separate facts supported by code from inferred intent.

4. Infer the requirement behind the implementation
   - Convert the behavior into user stories that are independently valuable.
   - Prioritize by business centrality, not source-file order.
   - Write functional requirements in technology-agnostic language.
   - Infer key entities only when they are meaningful in business terms.
   - Infer success criteria conservatively. Do not fabricate numeric targets.

5. Mark ambiguity explicitly
   - If the implementation leaves intent unclear, record it in `## Clarifications`.
   - Use `[NEEDS CLARIFICATION: ...]` for unresolved intent, thresholds, ownership, or unsupported assumptions.
   - Do not silently "fill in" product intent when the evidence is weak.

6. Draft or update the spec
   - Write a full SpecKit `spec.md`.
   - If a previous spec exists, preserve what is still accurate and update what is not.
   - Ensure the final result reads as one coherent current specification.

7. Validate before finishing
   - Confirm the final wording stays at the level of observable behavior and business meaning.
   - Confirm the key stories, constraints, and edge cases are supported by evidence.
   - Confirm unresolved ambiguity is labeled instead of guessed.

## Output Contract

The generated `spec.md` should follow this shape:

- `# Feature Specification: ...`
- feature metadata block
- `## Clarifications`
- `## User Scenarios & Testing`
- `### Edge Cases`
- `## Requirements`
- `### Functional Requirements`
- `### Key Entities` when applicable
- `## Success Criteria`

Write the spec primarily in Traditional Chinese (`zh-TW`).

## Output Template

Use the shared template at:

- `assets/spec-template.md`

Rules:

- Treat `assets/spec-template.md` as the single source of truth for the generated `spec.md` structure.
- Replace every placeholder with extracted content from the codebase, tests, and adjacent docs.
- Do not leave placeholder text, comments, or instructional text in the final file.

## Decision Rules

- Prefer implemented behavior over comments that appear outdated.
- Prefer tests over assumptions.
- Prefer domain language over transport or storage language.
- Prefer narrower, defensible claims over broad invented narratives.
- If a feature has multiple entry points that serve the same business workflow, combine them into one story set instead of duplicating them.
- If the implementation mixes multiple unrelated capabilities, split them into separate candidate specs and choose the one that best matches the requested boundary.

## When To Stop And Ask

Ask the user only if one of these is true:

- the requested target combines multiple unrelated features and no safe default boundary exists,
- the repository conventions for where specs should live are genuinely ambiguous,
- the codebase does not provide enough evidence to distinguish two materially different interpretations.

Otherwise, make the narrowest reasonable assumption and continue.

## Example Prompts

```text
Use the create-spec skill to extract the current account suspension feature from `internal/http/controller/admin_user` and related domain code, then write `docs/specs/004-admin-user-suspension/spec.md`.
```

```text
Use the create-spec skill to reverse-engineer the proxy peer workflow from `internal/pkg/proxy` and adjacent handlers. If the true business motivation is unclear, keep it in `## Clarifications` with `[NEEDS CLARIFICATION: ...]`.
```

```text
Use the create-spec skill on `backend/src/modules/billing`, infer a repository-consistent spec path under `docs/specs/`, and treat this hint as optional context: "This module is mainly for back-office reconciliation."
```

## Expected Agent Response Pattern

- Confirm the target boundary being extracted.
- Inspect code, tests, and adjacent docs before writing.
- Create or update the `spec.md`.
- Return the completion response required by the invoking prompt, client, or environment.

## Acceptance Checks

- [ ] The skill produces a full `spec.md`, not scattered notes.
- [ ] The resulting spec describes WHAT/WHY, not HOW.
- [ ] User stories are independently testable and derived from actual behavior.
- [ ] Ambiguities are labeled with `[NEEDS CLARIFICATION: ...]` instead of guessed away.
- [ ] No placeholder text from templates remains.
- [ ] The destination path is explicit and repository-consistent.

## Changelog

- `0.1.0`: Initial workspace skill for extracting SpecKit specs from existing implementations.
