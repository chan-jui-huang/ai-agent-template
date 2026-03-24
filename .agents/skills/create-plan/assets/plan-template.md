# Implementation Plan: [FEATURE NAME]

**Date**: [DATE]  
**Spec**: [SPEC PATH]  
**Input**: Existing implementation from [TARGET SUMMARY]

## Scope

- [Summarize the feature boundary and major implementation surfaces covered by this plan.]

## Source Alignment

### Code Truth Summary

- [Summarize the implementation as it exists today.]

### Spec Support Summary

- [Summarize how `spec.md` is used to frame stories, naming, and expected boundaries.]

## Technical Context

- [Summarize the architecture, shared components, persistence surfaces, integrations, and cross-cutting rules that materially affect multiple stories.]

## Project Structure

> Note: Fill this section using SpecKit's `Project Structure` as the baseline, but not every file or directory will necessarily exist. Keep only the items that actually exist and are relevant to the repository and feature boundary, and remove anything that is missing or not applicable.

### Documentation (this feature)

```text
[feature-doc-root]/
├── plan.md              # This file
├── spec.md              # Required input for this workflow
├── research.md          # Optional: may exist when prior planning research was produced
├── data-model.md        # Optional: may exist when data modeling was produced
├── quickstart.md        # Optional: may exist when validation scenarios were documented
├── contracts/           # Optional: may exist when interface contracts were produced
└── tasks.md             # Optional: may exist when implementation tasks were later generated
```

### Source Code (repository root)

```text
# [REMOVE IF UNUSED] Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# [REMOVE IF UNUSED] Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# [REMOVE IF UNUSED] Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure: feature modules, UI flows, platform tests]
```

**Structure Decision**: [Document the selected structure, replace the generic tree with real repository paths, and reference only the directories that actually exist and materially support this feature.]

## Story Dependency Order

1. [USER STORY 1]
2. [USER STORY 2]
3. [USER STORY 3]

## User Story Plans

### [USER STORY 1]

**Dependency Note**: [Explain why this story comes at this point in the dependency order.]

**Input**

- [List the inputs, preconditions, or triggering context.]

**Output**

- [List the outputs, resulting state, or externally visible outcome.]

**How Input Becomes Output**

[Describe the program logic in prose. Explain how the relevant modules transform the input into the output, including validation, authorization, persistence, side effects, and failure boundaries only when they materially shape the flow.]

**Implementation Surfaces**

- [List the major code surfaces or modules that participate in this story.]

**Data and Integration Notes**

- [Summarize the data relationships, tables, queues, jobs, external services, or other integrations involved. Write `- None.` if not applicable.]

### [USER STORY 2]

**Dependency Note**: [Explain why this story comes after earlier stories.]

**Input**

- [List the inputs, preconditions, or triggering context.]

**Output**

- [List the outputs, resulting state, or externally visible outcome.]

**How Input Becomes Output**

[Describe the program logic in prose.]

**Implementation Surfaces**

- [List the major code surfaces or modules that participate in this story.]

**Data and Integration Notes**

- [Summarize the data relationships or integrations involved.]

## Code vs Spec Conflicts

- [Describe each meaningful mismatch between the current code and `spec.md`. If none exist, state that no meaningful conflicts were identified.]

## Verification Focus

- [List the tests, scenarios, or observable behaviors that best confirm the reconstructed plan.]
