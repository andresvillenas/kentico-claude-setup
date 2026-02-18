---
description: "Scan an Xperience by Kentico project and generate a tailored CLAUDE.md."
allowed-tools: Bash, Glob, Grep, Read, Write, Edit, TodoWrite, mcp__kentico-docs__*, mcp__kentico-cm__*
---

# Document Heading

## Initialize Kentico AI Tooling

You are generating a `CLAUDE.md` for an **Xperience by Kentico** project. Follow these steps carefully and in order.

---

### Step 1: Detect project state

Check whether this is an existing XbyK project or a new/empty one:

- **Existing project:** at least one `.csproj` file exists containing `kentico.xperience.webapp`
- **New/empty project:** no `.csproj` files found, or none reference Kentico packages yet

If `.csproj` files exist but none reference `kentico.xperience.webapp`, tell the user this does not appear to be an Xperience by Kentico project and stop.

Set a mental flag: **IS_EXISTING_PROJECT** (true/false). The remaining steps branch on this.

---

### Step 2: Discover project structure (existing projects only)

**Skip this step if IS_EXISTING_PROJECT is false.** Proceed to Step 3.

Read the following to understand the project:

1. Find and read the solution file (`.sln`) to identify all projects
2. Read `Program.cs` (or `Startup.cs`) from the web project — note: registered services, content types in `UsePageBuilder()`
3. Read `.csproj` files — note: Kentico version, NuGet packages
4. Check `src/` or root for channel structure (look for `Channels/` folder or channel-named projects)
5. Check for existing `Models/Generated/` path for generated types
6. Check for test project

Collect the following facts:

- **Solution name** and **web project name**
- **Kentico Xperience version** (from package reference)
- **Target framework** (net10.0, net9.0, etc.)
- **Channels** (whatever channel-named projects exist)
- **Page Builder content types** (from `UsePageBuilder()` registration)
- **Custom services** registered (from `ConfigureCustomServices` or equivalent)
- **External APIs or integrations** (HttpClients, Polly, external packages)
- **Key folder paths**: web project, business/core project, channels, generated models, widgets, page templates
- **Test project**: name and framework
- **Notable community packages**: ContentRepository, CacheManager, SEO, Localization, etc.

---

### Step 3: Generate CLAUDE.md

Write a `CLAUDE.md` file at the **project root**. The content depends on the project state detected in Step 1.

---

#### If IS_EXISTING_PROJECT is true — generate a populated CLAUDE.md

Fill in all discovered facts:

```markdown
# [Solution/Project Name]

## Overview

[1-2 sentence description of what this project is. Include: organization name if identifiable, platform (Kentico Xperience [VERSION]), .NET version, and the channels it serves.]

## Architecture

\`\`\`
[Draw a tree showing the actual folder structure found, similar to:]
src/
  [WebProject]/           # ASP.NET Core web app (entry point, DI, routing)
  [BusinessProject]/      # Business logic, services, models
  [TestProject]/          # Unit tests ([framework], .[net version])
  Channels/
    [ChannelA]/           # [channel name] channel
    [ChannelB]/           # [channel name] channel
    Shared.Channel/       # Shared Razor class library (if present)
\`\`\`

## Build & Run

\`\`\`bash
dotnet build [path/to/solution.sln]
dotnet test [path/to/TestProject]
dotnet run --project [path/to/WebProject]
\`\`\`

### Code Generation

[If generated models path was found, include:]
Generated models live in `[path/to/Models/Generated/]`. **Never edit generated files** — they are overwritten by:

\`\`\`bash
dotnet rebuild [WebProject] -p:EnableKenticoCodeGeneration=true
\`\`\`

## Key Patterns

[List only patterns confirmed present in this project:]
- **DI everywhere** — services registered in `Program.cs` via `[function name]()`
- **View Components for widgets** — in `[path to widgets]/`
- **Page Templates** — in `[path to templates]/`
- **Content retrieval** — via [XperienceCommunity.ContentRepository if present, or standard Kentico providers]
[Add others only if confirmed: Caching, Localization, Search, External APIs, SEO]

## Coding Conventions

- C# with `<Nullable>enable</Nullable>` and `<ImplicitUsings>enable</ImplicitUsings>`
- Always validate null/empty on content item properties before use
- Use `nameof()` and constants instead of magic strings
[Add project-specific conventions discovered, e.g. cookie consent patterns, API auth patterns]

## Kentico Xperience Version

- **Xperience [VERSION]** (XbyK — Xperience by Kentico)
- Page Builder with content types: [list content types from UsePageBuilder()]
- Template filters per content type [if ConfigureTemplateFilters or equivalent found]

## MCP Servers

This project has Kentico MCP servers configured (see `.mcp.json`):

- **kentico-mcp** — Search and fetch Kentico documentation. Use when unsure about APIs or features.
- **kentico-cm-mcp** — Content modeling workflow for designing content types.

When working on Kentico-specific features, prefer searching docs via MCP over guessing.
```

---

#### If IS_EXISTING_PROJECT is false — generate a starter CLAUDE.md with placeholders

The project is new or empty. Generate a template the developer will fill in as they build. **Do not assume any folder structure or architecture** — leave those as explicit placeholders:

```markdown
# [Project Name]

## Overview

[Describe this project: organization, purpose, target audiences/channels.]

This project is built on **Xperience by Kentico** (XbyK) with .NET.

## Architecture

> Fill this in once the project is scaffolded. Run `/init-kentico` again to auto-generate it from your actual structure.

\`\`\`
[your project structure here]
\`\`\`

## Build & Run

> Fill these in once the project is scaffolded.

\`\`\`bash
dotnet build [path/to/solution.sln]
dotnet test [path/to/TestProject]
dotnet run --project [path/to/WebProject]
\`\`\`

### Code Generation

> Fill in the generated models path once known.

\`\`\`bash
dotnet rebuild [WebProject] -p:EnableKenticoCodeGeneration=true
\`\`\`

## Key Patterns

> Fill these in as the project takes shape.

- **DI everywhere** — services registered in `Program.cs`
- **Content retrieval** — via Kentico typed content providers
- [Add more as discovered]

## Coding Conventions

- C# with `<Nullable>enable</Nullable>` and `<ImplicitUsings>enable</ImplicitUsings>`
- Always validate null/empty on content item properties before use
- Use `nameof()` and constants instead of magic strings
- Use Combined Content Selector (not Web Page Selector) for linked content in widgets

## Kentico Xperience Version

- **Xperience by Kentico** (XbyK)
- Version: [fill in]
- Page Builder content types: [fill in after scaffolding]

## MCP Servers

This project has Kentico MCP servers configured (see `.mcp.json`):

- **kentico-mcp** — Search and fetch Kentico documentation. Use when unsure about APIs or features.
- **kentico-cm-mcp** — Content modeling workflow for designing content types.

When working on Kentico-specific features, prefer searching docs via MCP over guessing.
```

Tell the user: **"This is a starter CLAUDE.md with no assumed structure. Fill in the placeholders as you scaffold the project, or run `/init-kentico` again once the project exists to auto-generate it from the real structure."**

---

### Step 4: Report

Tell the user:

1. Whether this was detected as an existing project or a new/empty one
2. If existing: key facts discovered (Kentico version, channels, content types, notable packages)
3. If new/empty: remind them to update `CLAUDE.md` placeholders as they build out the project

**Next steps (always include):**

- Reload Claude Code to activate MCP servers
- To design a content model before writing code: ask Claude to "start content modeling" (uses kentico-cm-mcp)

**Next steps (existing projects only):**

- To create a widget: prepare a folder with `requirements.md`, then run `/widget-create-research [folder-path]`

**Next steps (new/empty projects only):**

- Scaffold the project: `dotnet new` or follow the Kentico setup guide
- Once scaffolded, run `/init-kentico` again to regenerate `CLAUDE.md` with real project details
