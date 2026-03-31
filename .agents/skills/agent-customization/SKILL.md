---
name: agent-customization
description: Create an agent skill customization file (SKILL.md) that packages a workflow.
compatibility: Requires access to the workspace files where the skill will be created or updated.
metadata:
  version: '0.2.0'
---

# Creating SKILL.md

## Overview

Use this skill when you want to turn a conversation, repeated workflow, or manual operating pattern into a reusable skill.

The final result should be a reusable skill under `.agents/skills/<skill-name>/`.

## Inputs

- `goal`: what the skill should produce or automate
- conversation context, issue text, or workflow notes when available
- optional target path if the destination should be fixed up front

## Instructions

1. Read `references/workflow.md` and follow it as the authoritative runbook for this skill. **You must follow it in full.**
2. Create or update the skill under `.agents/skills/<skill-name>/SKILL.md` unless the user asks for a different valid skill root.
3. Follow the Agent Skills format: valid frontmatter, concise body instructions, and referenced files only when they materially improve reuse.
4. Keep the main `SKILL.md` concise and move longer supporting guidance into `references/` only when needed.
5. Include at least one runnable example prompt that shows how the resulting skill should be invoked.

## Referenced Files

- `references/workflow.md`

## Example prompts

```text
Create a skill that packages our PR review workflow into `.agents/skills/pr-review/SKILL.md`, including an example prompt and any supporting reference files that are actually needed.
```

```text
Turn this conversation's release checklist into a reusable workspace skill. Keep the main `SKILL.md` concise and move long guidance into `references/` if necessary.
```

## Changelog

- `0.2.0`: Consolidate the create-skill prompt into the workspace skill and align the skill frontmatter with the Agent Skills specification.
