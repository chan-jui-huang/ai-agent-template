---
description: Extract and infer a SpecKit `spec.md` from an existing codebase, current behavior, and any optional user hints.
argument-hint: [TARGET="<files|dirs|module>"] [SPEC_PATH="<docs/specs/.../spec.md>"] [FEATURE_NAME="<feature name>"] [HINT="<optional business context>"]
---

You are generating or updating a SpecKit feature specification for an existing feature or module.

Your job is to reverse-engineer the feature from the current codebase and produce a high-quality `spec.md` that explains:

- What the feature does today
- Why this feature likely exists
- What users or operators can observe externally
- What business rules and constraints can be derived from the implementation
- Which parts are clear, and which parts still need confirmation

This workflow is for **spec extraction**, not greenfield invention.

If the workspace already provides a dedicated create-spec skill for this workflow, use that skill as part of the execution flow and keep this prompt aligned with that skill's process and shared template.

You MUST treat the codebase, existing docs, and current runtime behavior as the primary source of truth.
If the user provides extra context, use it as a secondary input that can clarify intent, priority, or domain terms, but do not let it overwrite clear evidence from the code without calling out the mismatch.

## User Input

```text
$ARGUMENTS
```

## Required Outcome

Create or update a SpecKit-style `spec.md` file at the requested path.

If the user does not provide a concrete output path, prefer a reasonable path under `docs/specs/`.
Only fall back to another repository-consistent location if there is clear evidence that `docs/specs/` is not the correct convention in the current workspace.

Your reply MUST NOT paste the full `spec.md` content.

## What To Inspect First

Before writing the spec, inspect the most relevant sources you can find, prioritizing:

1. Existing `spec.md` for the same feature, if any
2. Adjacent plan / research / contracts / quickstart docs for that feature, if any
3. Entry points and interfaces
   - including but not limited to: HTTP routes, controllers, handlers, RPC endpoints, CLI commands, jobs, UI flows
4. Business logic modules
   - including but not limited to: services, use cases, domain packages, validators, policies
5. Data definitions that reveal domain meaning
   - Models, schemas, migrations, enums, seed data
6. Tests that demonstrate intended or protected behavior
7. README, ADR, comments, or operational docs that clarify domain language

If the user gives a target module or directory, start there and expand outward only as needed.

## Source-of-Truth Rules

- Prefer implemented behavior over assumptions.
- Prefer tests and shared business logic over thin transport adapters.
- Prefer explicit validation, branching, permissions, and persistence rules over vague naming guesses.
- If code and docs disagree:
  - preserve the implemented behavior as the current truth,
  - mention the ambiguity using `[NEEDS CLARIFICATION: ...]` where appropriate.
- If behavior is only partially inferable:
  - make the narrowest defensible inference,
  - label uncertain statements with `[NEEDS CLARIFICATION: ...]`,
  - do not invent missing requirements just to make the spec look complete.

## Core Distinction From Standard SpecKit Authoring

Normal SpecKit authoring starts from a requested feature.
This workflow starts from an **existing implementation** and must extract the probable product requirement behind it.

That means you MUST:

- infer the likely user journey from the code,
- infer business terminology from naming, validation, tests, and docs,
- infer requirement priority from how central and protected each flow is,
- preserve uncertainty honestly when the code does not fully explain intent.

Optional user hints may be used for:

- domain naming,
- feature boundaries,
- known business motivation,
- missing background not obvious from code.

Optional user hints are NOT required.

## What The Spec Must And Must Not Contain

The resulting `spec.md` must follow SpecKit's purpose:

- focus on **WHAT** and **WHY**
- define user scenarios, requirements, key entities, and success criteria
- remain implementation-agnostic in the final wording

The resulting `spec.md` must NOT:

- mention functions, methods, classes, packages, tables, migrations, or file names
- describe frameworks, libraries, architecture, or technical stack choices
- dump request / response schemas, JSON shapes, SQL structure, or raw field lists without business meaning
- pretend uncertain intent is certain

You may inspect technical details internally, but convert them into business-facing language in the final file.

## Spec Structure

Write the full file in Markdown by following the shared template at:

- `.agents/skills/create-spec/assets/spec-template.md`

Use that file as the single source of truth for section order, metadata block, and placeholder shape.

Rules for using the shared template:

- Replace every placeholder with extracted content from the codebase, tests, and adjacent docs.
- Do not leave template placeholders, comments, or instructional text in the final `spec.md`.
- If there are no meaningful ambiguities, keep the `Clarifications` section but replace ambiguity examples with:
  - `- No additional clarifications were identified during this extraction.`
- If the template and any older local spec examples differ, prefer the shared template.

## How To Derive Each Section

### 1. Feature Name / Slug

- Reuse an existing feature folder name if present.
- Otherwise infer a concise slug from the module or capability being extracted.
- Prefer business/domain names over technical names.

### 2. Clarifications

- This section is allowed even during extraction.
- Use it to record unresolved ambiguities discovered from the code.
- If you do not have a confirmed answer, keep the answer as `[NEEDS CLARIFICATION: ...]`.
- Do not fabricate clarifications that were never derived from the source material.
- If there are no meaningful ambiguities, include:
  - `- No additional clarifications were identified during this extraction.`

### 3. User Stories & Acceptance Scenarios

Each user story must represent an independently valuable slice of behavior that already exists in the system.

Derive stories from the implemented behaviors that deliver distinct user or operator value.
Group related actions into coherent journeys, and separate them only when they lead to materially different goals, outcomes, or constraints.
Relevant sources for story extraction include, but are not limited to:

- end-to-end operational flows
- business-critical actions
- approval, permission, or eligibility boundaries
- state-dependent flows
- high-value retrieval or management experiences

Prioritize stories by business centrality, not by code order.

Acceptance scenarios must cover the observable outcomes, constraints, and decision paths that define each story.
Include enough scenarios to describe how the story succeeds, how it fails, and how it behaves under materially different conditions.
Relevant scenario types include, but are not limited to:

- the primary successful path
- meaningful failure or rejection paths
- state- or input-dependent branches
- access-control or eligibility outcomes
- dependency-related outcomes when they affect what users or operators see

Do not create stories for tiny helper mechanics unless they are directly meaningful to a user or operator.

### 4. Edge Cases

List real edge cases evidenced by the implementation or tests.
Include cases that reveal unusual limits, alternative paths, or behaviors that differ from the primary expected flow.
Relevant edge case types include, but are not limited to:

- boundary or limit conditions
- empty, missing, duplicate, or repeated inputs or results
- partial-completion, fallback, retry, or rollback behavior
- state-dependent restrictions, transitions, or recovery paths
- unusual combinations of otherwise valid inputs
- non-primary success outcomes that still produce a valid result

### 5. Functional Requirements

Convert extracted behavior into technology-agnostic requirement statements.

Rules:

- Use `System MUST ...` or `Users MUST be able to ...`
- Capture only externally meaningful behavior
- Use `[NEEDS CLARIFICATION: ...]` when intent, scope, or thresholds cannot be confirmed
- Include validation, authorization, visibility, and consistency rules when they are externally meaningful

### 6. Key Entities

Include only if the feature clearly revolves around business entities.

Describe:

- what each entity represents,
- its role in the workflow,
- important relationships in business terms.

Do not describe table design or code structs.

### 7. Success Criteria

Infer measurable outcomes carefully.

Allowed sources:

- explicit business metrics from docs,
- operational expectations implied by UX flow,
- observable completion quality from acceptance tests.

If no trustworthy quantitative target exists, use conservative qualitative-but-testable criteria and mark uncertainty where needed.

Bad:

- made-up traffic numbers or latency goals

Good:

- `SC-001`: Users can complete the primary flow in a single pass and immediately see a recognizable success result.
- `SC-002`: When input does not satisfy the rules, the system clearly identifies what must be corrected instead of accepting incomplete data.

## Writing Rules

- The final `spec.md` content must be primarily in Traditional Chinese (`zh-TW`).
- Use clear business language for non-engineering readers.
- Keep necessary proper nouns if they are domain terms.
- Avoid raw implementation terminology unless it is the only stable domain term available.

## Extraction Heuristics

Use heuristics that help infer the real user-visible intent behind the implementation.
Relevant heuristics include, but are not limited to:

- validation and normalization logic often reveal required inputs, accepted variations, and business constraints
- access-control, eligibility, or role-based checks often reveal intended actors and feature boundaries
- completion, retry, fallback, rollback, or recovery behavior often reveal expectations around consistency and failure handling
- output composition, display logic, or returned aggregates often reveal which information users need together
- shared constants, fixtures, seed data, example payloads, or reference values often reveal stable domain vocabulary
- tests often reveal the most important behaviors, decision paths, and edge cases

## Updating Existing Specs

If a `spec.md` already exists:

- preserve sections that still match the implementation,
- rewrite sections that drift from the code,
- fill obvious gaps exposed by new logic or tests,
- do not keep outdated statements just because they were already written.

The result should read like one coherent current specification, not a patchwork diff.

## Minimum Quality Bar

Before finishing, verify internally that the generated spec:

- reflects actual implemented behavior,
- separates clear facts from inferred or unclear intent,
- includes independently testable user stories,
- contains no HOW-level implementation details,
- does not leave placeholder template text behind,
- uses consistent business terminology throughout.

## Output Rules

- Do NOT print the full `spec.md` content in your reply.
- Your reply must contain only:
  - `Completed`
  - the final path to `spec.md`

Nothing else.
