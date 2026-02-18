# Kentico Claude Setup

AI-assisted development tooling for **Xperience by Kentico** projects, powered by **Claude Code**.

Includes:
- MCP servers for Kentico documentation and content modeling
- Custom slash commands for Page Builder widget creation (research + implementation)
- Page Builder rules, documentation links, and widget examples for Claude context
- A bootstrap command that auto-generates a tailored `CLAUDE.md` for any XbyK project

---

## Quick Install (one file)

This is the easiest way. Copy a single file, run one command, everything is scaffolded automatically.

**1. Copy the bootstrap command to your project:**

```
files/.claude/commands/init-kentico.md  →  <your-project>/.claude/commands/init-kentico.md
```

**2. Open Claude Code in VS Code (or run `claude` in the terminal) at your project root.**

**3. Run:**

```
/init-kentico
```

Claude Code will:
- Scan your project structure (`Program.cs`, `.csproj`, channels, etc.)
- Generate a tailored `CLAUDE.md` for your project
- Write `.mcp.json` with the Kentico MCP servers
- Create all supporting commands, instructions, and templates
- Update `.claude/settings.local.json` with the required permissions

**4. Reload Claude Code** (close and reopen, or run `/mcp` to verify servers connected).

---

## Manual Install (all files)

If you prefer to copy everything yourself and only need the `CLAUDE.md` generated:

**1. Copy the contents of `files/` to your project root:**

```
files/
  .mcp.json                              →  <project>/.mcp.json
  .claude/commands/init-kentico.md       →  <project>/.claude/commands/init-kentico.md
  .claude/commands/widget-create-research.md
  .claude/commands/widget-create-implementation.md
  .claude/instructions/base-pagebuilder.md
  .claude/instructions/docs.md
  .claude/instructions/example-widgets.md
  .claude/create-instructions/CREATION_TEMPLATE.md
```

**2. Run `/init-kentico`** to generate the project-specific `CLAUDE.md`.

Or write `CLAUDE.md` manually — see [CLAUDE.md template structure](#claudemd-structure) below.

**3. Update `.claude/settings.local.json`** to allow MCP tools without prompting:

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
| `/init-kentico` | `/init-kentico` | Bootstrap: scan project, generate CLAUDE.md, scaffold all files |
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

## CLAUDE.md Structure

The `CLAUDE.md` at the project root is the main instruction file Claude Code reads automatically. It is **project-specific** and not included as a static file — `/init-kentico` generates it by scanning your project.

It should contain: project overview, architecture tree, build commands, key patterns, coding conventions, Kentico version, and MCP server references.

---

## Requirements

- Xperience by Kentico 30.6.0 or newer
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI or VS Code extension)

---

## Based On

- [KenticoPilot](https://github.com/Kentico/xperience-by-kentico-kenticopilot) by Kentico — the original prompts and widget creation workflow
- [Kentico Documentation MCP Server](https://docs.kentico.com/documentation/developers-and-admins/installation/mcp-server)
- [Kentico Content Modeling MCP Server](https://docs.kentico.com/guides/architecture/content-modeling/content-modeling-mcp)
