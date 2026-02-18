# Kentico Claude Setup

AI-assisted development tooling for **Xperience by Kentico** projects, powered by **Claude Code**.

Includes:

- MCP servers for Kentico documentation and content modeling
- Custom slash commands for Page Builder widget creation (research + implementation)
- Page Builder rules, documentation links, and widget examples for Claude context
- A bootstrap command (`/init-kentico`) that auto-generates a tailored `CLAUDE.md` for any XbyK project

---

## Install

### Option 1: ai4dev (recommended)

[ai4dev](https://github.com/SimpleA/ai4dev) is a CLI tool for distributing AI tooling packages. It copies all files to your project in one command.

```bash
ai4dev install kentico
```

Then run `/init-kentico` in Claude Code to generate your project's `CLAUDE.md`.

### Option 2: Manual

Copy the contents of `files/` to your project root:

```
files/
  .mcp.json                                        →  <project>/.mcp.json
  .claude/commands/init-kentico.md                 →  <project>/.claude/commands/
  .claude/commands/widget-create-research.md       →  <project>/.claude/commands/
  .claude/commands/widget-create-implementation.md →  <project>/.claude/commands/
  .claude/instructions/base-pagebuilder.md         →  <project>/.claude/instructions/
  .claude/instructions/docs.md                     →  <project>/.claude/instructions/
  .claude/instructions/example-widgets.md          →  <project>/.claude/instructions/
  .claude/create-instructions/CREATION_TEMPLATE.md →  <project>/.claude/create-instructions/
```

Then run `/init-kentico` in Claude Code to generate your project's `CLAUDE.md`.

---

## After Installing

**1. Add MCP permissions** to `.claude/settings.local.json` so Claude Code can use the Kentico MCP servers without prompting:

```json
{
  "permissions": {
    "allow": [
      "mcp__kentico-mcp__*",
      "mcp__kentico-cm-mcp__*"
    ]
  }
}
```

**2. Run `/init-kentico`** in Claude Code. It scans your project and writes a tailored `CLAUDE.md`. That's all it does — the rest of the files are already in place from the install step.

**3. Reload Claude Code** (close and reopen, or run `/mcp` to verify servers connected).

---

## What's Included

### MCP Servers (`.mcp.json`)

| Server | URL | Purpose |
| ------ | --- | ------- |
| `kentico-mcp` | `https://docs.kentico.com/mcp` | Search and fetch Kentico docs in-context |
| `kentico-cm-mcp` | `https://ai.kentico.com/content-model-mcp` | 5-phase content modeling workflow |

### Slash Commands

| Command | Usage | Description |
| ------- | ----- | ----------- |
| `/init-kentico` | `/init-kentico` | Scan project and generate a tailored `CLAUDE.md` |
| `/widget-create-research` | `/widget-create-research ./my-widget/` | Research stage: reads docs + project, fills in creation template |
| `/widget-create-implementation` | `/widget-create-implementation ./my-widget/instructions.md` | Implementation stage: builds the widget from the filled template |

### Widget Creation Workflow

1. Create a folder (e.g., `widget-specs/hero-widget/`) with a `requirements.md` describing your widget
2. Optionally add a `design.html` or `design.md` with visual specs
3. Run `/widget-create-research ./widget-specs/hero-widget/` — Claude reads docs + project patterns, fills in the creation template
4. Review the generated instructions file
5. Run `/widget-create-implementation ./widget-specs/hero-widget/instructions.md` — Claude implements the widget

### Content Modeling Workflow

Ask Claude to **"start content modeling"** — it invokes the `kentico-cm-mcp` server's 5-phase workflow:

1. Requirements Gathering & Approach Selection
2. Content Type Design
3. Relationship Design
4. Page Builder Templates & Sections
5. Final Validation & Deliverables

---

## CLAUDE.md

The `CLAUDE.md` at the project root is the main instruction file Claude Code reads automatically. It is **project-specific** and not included as a static file — `/init-kentico` generates it by scanning your project structure, `.csproj` files, `Program.cs`, and channel folders.

---

## Requirements

- Xperience by Kentico 30.6.0 or newer
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI or VS Code extension)

---

## Based On

- [KenticoPilot](https://github.com/Kentico/xperience-by-kentico-kenticopilot) by Kentico — the original prompts and widget creation workflow
- [Kentico Documentation MCP Server](https://docs.kentico.com/documentation/developers-and-admins/installation/mcp-server)
- [Kentico Content Modeling MCP Server](https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-mcp)
