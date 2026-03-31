# Create-Plan Workflow Runbook

Use this runbook when generating or updating a SpecKit-style implementation plan for an existing feature or module.

Your job is to reverse-engineer the current implementation and produce a high-quality `plan.md` that explains, in dependency order:

- how the current system is actually built,
- how each `spec.md` user story maps to concrete implementation work,
- what inputs each story-level flow consumes,
- what outputs each story-level flow produces,
- how the program transforms those inputs into outputs,
- which cross-cutting technical decisions support the feature today,
- and where the code and `spec.md` disagree.

This workflow extracts a development plan from an existing codebase.
It does not invent a new plan from scratch.

You MUST treat the codebase as the primary source of truth.
You MUST treat the user-provided `spec.md` as a secondary source that helps recover user-story boundaries, terminology, and intended sequencing.
Optional user hints about the plan may refine naming or emphasis, but they are not required and must not override clear evidence from the code.

## Required Inputs

The user MUST provide:

- the corresponding `spec.md`, and
- the relevant implementation context, including one or more files and/or directories.

If either the spec or the implementation boundary is missing, stop and ask for the missing input.

Use `plan_hint` only if the user supplies it.
Treat it as optional context because the user may not know the real implementation plan.

## Required Outcome

Create or update a SpecKit-style `plan.md` file at the requested path.

If the user does not provide a concrete output path, prefer a repository-consistent path next to the provided `spec.md`.

Do not paste the full `plan.md` content in the completion message unless the invoking environment explicitly requires it.

## What To Inspect First

Before writing the plan, inspect the most relevant sources you can find, prioritizing:

1. the target `spec.md`,
2. an existing `plan.md` for the same feature, if any,
3. the code paths that implement each user story,
4. shared logic, data access, and integration boundaries that shape real behavior,
5. tests that confirm flow order, branching, and failure handling,
6. adjacent docs that clarify technical context, assumptions, or constraints.

If the user gives a target module or directory, start there and expand outward only as needed.

## Source-of-Truth Rules

- Prefer implemented behavior over undocumented intent.
- Prefer tests and shared business logic over thin adapters.
- Prefer real call order, dependency order, validation order, and persistence order over naming guesses.
- If code and `spec.md` disagree:
  - preserve the implementation as the current truth in `plan.md`,
  - record the conflict explicitly in the plan.
- If the implementation only partially supports a `spec.md` story:
  - describe only the supported portion as current truth,
  - mark uncertainty or mismatch explicitly,
  - do not invent missing technical steps to "complete" the story.

## Core Distinction From Standard SpecKit Planning

Normal SpecKit planning starts from a validated feature specification and designs a future implementation.

This workflow starts from an existing implementation and must reconstruct the effective development plan that the code already embodies.

That means you MUST:

- use `spec.md` user stories as the organizing frame,
- order the stories by implementation dependency,
- derive the actual technical flow for each story from the code,
- describe each story in terms of input, output, and transformation logic,
- keep the final plan high-level and implementation-oriented,
- record code/spec conflicts instead of forcing false consistency.

## What The Plan Must And Must Not Contain

The resulting `plan.md` must:

- stay focused on implementation strategy and technical flow,
- use code as the primary evidence,
- treat `spec.md` as supporting context,
- include the relevant documentation and source-code structure for the feature boundary,
- include story-by-story plans in dependency order,
- include `Input`, `Output`, and a prose description of how input becomes output for every story plan,
- record conflicts between code and `spec.md`,
- remain high-level rather than becoming a task checklist or code dump.

The resulting `plan.md` must NOT:

- invent missing architecture, packages, schemas, or flows,
- restate the entire `spec.md` as product prose,
- devolve into low-level file-by-file patch instructions,
- hide ambiguities or code/spec mismatches.

## Shared Template

Write the full file in Markdown by following the shared template at:

- `.agents/skills/create-plan/assets/plan-template.md`

Use that file as the single source of truth for section order and placeholder shape.

Rules for using the shared template:

- Replace every placeholder with extracted content.
- Do not leave template placeholders, comments, or instructional text in the final `plan.md`.
- If no meaningful conflicts are found, keep the conflict section and state that none were identified.
- If the template and older local plan examples differ, prefer the shared template.

## Writing Rules

- Write the final `plan.md` primarily in Traditional Chinese (`zh-TW`).
- Keep externally visible identifiers, file paths, route paths, table names, field names, and code identifiers exactly as they appear in the source material.
- Keep the writing high-level and abstract enough to explain architecture and flow, not line-level coding steps.
- Use concise Markdown headings and lists where they improve scanning.

## Completion Rules

- Follow any response-format contract defined by the invoking environment.
- If no stricter contract exists, return a concise completion message with the final `plan.md` path.
