# Agent-Customization Workflow Runbook

Use this runbook when creating or refining a reusable skill from a conversation, manual workflow, or repeated operating pattern.

## Extract from Conversation
First, review the conversation history or any provided workflow material. If the user has been following a multi-step workflow or methodology, generalize that into a reusable skill. Extract:
- The step-by-step process being followed
- Decision points and branching logic
- Quality criteria or completion checks

## Clarify if Needed
If no clear workflow emerges from the conversation, clarify:
- What outcome should this skill produce?
- Workspace-scoped or personal?
- Quick checklist or full multi-step workflow?

## Draft the Skill

When drafting the skill:

- Place it under `.agents/skills/<skill-name>/SKILL.md` unless the user requests a different valid skill root.
- Follow the Agent Skills specification for frontmatter:
  - `name` and `description` are required
  - `compatibility`, `metadata`, `license`, and `allowed-tools` are optional
  - `metadata` should be a string-to-string map
- Keep the main `SKILL.md` concise and task-focused.
- Move longer supporting guidance into `references/` when it would otherwise make `SKILL.md` bulky.
- Add `assets/` or `scripts/` only when they provide clear reuse value.
- Include at least one runnable example prompt.

## Validate Against the Spec

Before finishing, verify that the resulting skill:

- matches the parent directory name in the `name` field
- uses valid Agent Skills frontmatter
- clearly describes what the skill does and when to use it
- keeps the main `SKILL.md` short enough for efficient loading
- references supporting files with relative paths from the skill root

## Iterate
1. Draft the skill and save it.
2. Identify the most ambiguous or weak parts and ask about those.
3. Once finalized, summarize what the skill produces, suggest example prompts to try it, and propose related customizations to create next.

## Governing References

The resulting skill **must align** with these references:

- https://agentskills.io/what-are-skills
- https://agentskills.io/specification
