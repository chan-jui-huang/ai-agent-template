## Project Overview

This is an AI agent template repository focusing on configuration and prompts for AI development tools. The repository contains configuration for both Codex CLI and Gemini AI assistant.

## Repository Structure

```
.codex/
  config.toml          # Codex CLI runtime configuration
  prompts/
    commit.md          # Commit message generation workflow

.gemini/
  settings.json        # Gemini MCP servers and UI settings
  system.md            # Core system prompt for Gemini
  git_commit_spec.md   # Conventional Commits specification
  .env.example         # Environment variable template
  .env                 # Local secrets (gitignored)
```

## Environment Setup

```bash
# Copy environment template and configure secrets
cp .gemini/.env.example .gemini/.env
```

Required environment variables in `.gemini/.env`:
- `GEMINI_SYSTEM_MD` - Path to system prompt file (default: `.gemini/system.md`)
- `GITHUB_PERSONAL_ACCESS_TOKEN` - GitHub PAT for GitHub MCP server access

## Codex CLI Configuration

Configuration file: `.codex/config.toml`

- **Model**: `gpt-5-codex`
- **Sandbox mode**: `workspace-write`
- **Approval policy**: `on-failure`
- **MCP Servers**:
  - **context7**: Upstash Context7 MCP server via `npx -y @upstash/context7-mcp@latest`

## Gemini Configuration

Configuration file: `.gemini/settings.json`

### MCP Servers
- **github**: GitHub API via https://api.githubcopilot.com/mcp/ (requires `GITHUB_PERSONAL_ACCESS_TOKEN`)
- **context7**: Upstash Context7 via stdio (npx)

### Context Files
- Automatically loads: `AGENTS.md` (specified in `context.fileName`)

### Preferences
- Editor: VS Code
- Auth: OAuth personal

## Commit Message Guidelines

This repository strictly follows the **Conventional Commits 1.0.0** specification (https://www.conventionalcommits.org/en/v1.0.0/).

### Format
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Common Commit Types
- `feat:` - New feature (MINOR version)
- `fix:` - Bug fix (PATCH version)
- `docs:` - Documentation changes
- `refactor:` - Code refactoring
- `test:` - Test additions/modifications
- `chore:` - Build/tooling changes
- `perf:` - Performance improvements
- Add `!` after type/scope for breaking changes (MAJOR version)

### Commit Workflow

From `.codex/prompts/commit.md`:

1. Stage your changes: `git add .`
2. Request commit message generation from AI assistant
3. AI will analyze conversation history + `git diff` to generate appropriate message

**Important rules**:
- **Never** mention AI tools, models, or agents in commit messages (no Claude, Codex, Gemini, ChatGPT, etc.)
- Commit messages must only describe code/file changes
- Use single quotes for multi-line commit messages
- Escape single quotes within messages using bash syntax: `'\''`
- Subject line ≤72 characters
- Use present tense
- Write in English

### Example Commits from History
```
feat(config): refactor Gemini settings structure
docs: add commit workflow prompt
feat: add Codex CLI configuration
docs(spec): Refine commit message generation guidelines
```

## Gemini System Prompt Principles

The `.gemini/system.md` defines core agent behavior. Key principles:

### Core Mandates
- **Convention-first**: Analyze existing code patterns before making changes
- **Never assume libraries**: Verify library usage from package manifests (package.json, requirements.txt, etc.)
- **Mimic style**: Follow existing formatting, naming, structure, and architecture
- **Sparse comments**: Only add high-value comments explaining "why", not "what"
- **No summary spam**: Don't provide summaries after code modifications unless asked

### Development Workflow
1. **Understand**: Search and read files to understand context and conventions
2. **Plan**: Build grounded plan based on actual codebase analysis
3. **Implement**: Execute using available tools, adhering to project conventions
4. **Verify (Tests)**: Run project-specific test commands
5. **Verify (Standards)**: Run linting, type-checking, and build commands

### Tool Usage Guidelines
- Always use absolute paths (not relative)
- Execute independent tool calls in parallel
- Explain critical commands before execution
- Use non-interactive command versions (e.g., `npm init -y` not `npm init`)
- Avoid interactive commands like `git rebase -i`

### Tone
- Concise and direct (≤3 lines of text per response when practical)
- No chitchat, preambles, or postambles
- Professional CLI-appropriate communication
- Use tools for actions, text only for communication

## Security

- Never commit secrets or API keys
- Keep `.env` files out of version control (already in `.gitignore`)
- Store credentials in `.gemini/.env` and reference via environment variables
- Review MCP server commands for least-privilege access

## Git Workflow

Standard Git operations follow **Conventional Commits 1.0.0**:
- All commit messages in English
- Present tense descriptions
- Include scope when relevant: `feat(config): description`
- Use footers for references: `Refs: #123`
- Breaking changes: Use `!` or `BREAKING CHANGE:` footer
