---
description: "Bootstrap Kentico AI tooling for an Xperience by Kentico project. Scans the project and generates CLAUDE.md and all supporting files."
allowed-tools: Bash, Glob, Grep, Read, Write, Edit, TodoWrite, mcp__kentico-docs__*, mcp__kentico-cm__*
---

## Initialize Kentico AI Tooling

You are setting up AI-assisted development for an **Xperience by Kentico** project. Follow these steps carefully and in order.

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

### Step 4: Write .mcp.json

Write `.mcp.json` at the project root **only if it does not already exist**:

```json
{
  "mcpServers": {
    "kentico-mcp": {
      "type": "http",
      "url": "https://docs.kentico.com/mcp"
    },
    "kentico-cm-mcp": {
      "type": "http",
      "url": "https://ai.kentico.com/content-model-mcp"
    }
  }
}
```

---

### Step 5: Scaffold .claude/ support files

Write the following files **only if they do not already exist**. Do not overwrite existing files.

#### .claude/commands/widget-create-research.md

```markdown
---
description: "Prompt that helps with preparation of Widget creation process."
argument-hint: "[userInputFolderPath]"
allowed-tools: Bash, Glob, Grep, Read, Edit, Write, NotebookEdit, WebFetch, TodoWrite, WebSearch, AskUserQuestion, mcp__kentico-mcp__*, mcp__kentico-cm-mcp__*
---

## You are tasked with the process of creating a new prompt for generating a new widget.

### Input Parameters

- **User Input Folder Path:** `$ARGUMENTS` - The path to the folder that contains user input files with requirements and design for the new widget. You must follow these when creating the final prompt.

### Steps to follow

- First, check all documentation links in the `./.claude/instructions/docs.md` file using the kentico-mcp MCP server.

- Next, read all remaining files in the `./.claude/instructions/` folder.

- Then, check all requirements and design files in the user-input folder, whose path the user has provided to you.

- Check the current state of the project for resources you will need for creation of the widget. If you find already present widgets, follow their patterns and conventions.

- Finally, create a new instructions file in the user-input folder that will allow you to generate a new widget. Use `./.claude/create-instructions/CREATION_TEMPLATE.md` as a base and fill in all the parts in brackets. Other parts of the file must stay the same as in the template.
```

#### .claude/commands/widget-create-implementation.md

```markdown
---
description: "Prompt that helps with implementation of Widget creation process."
argument-hint: "[pathToCreationInstructionsFile]"
allowed-tools: Bash, Glob, Grep, Read, Edit, Write, NotebookEdit, WebFetch, TodoWrite, WebSearch, AskUserQuestion, mcp__kentico-mcp__*, mcp__kentico-cm-mcp__*
---

## You are tasked with implementing a new Page Builder widget for Xperience by Kentico.

### Input Parameters

- **Creation Instructions File Path:** `$ARGUMENTS` - Path to the filled-in creation instructions file generated by the research stage.

### Steps to follow

1. Read the creation instructions file at the provided path thoroughly.

2. Read all files in `./.claude/instructions/` to understand the project conventions and Kentico APIs.

3. Check the current state of the project — find existing widgets in the project and follow their exact patterns and conventions for:
   - File locations and naming
   - Namespace conventions
   - View component structure (`InvokeAsync` signature, return type)
   - Properties class structure (`IWidgetProperties`)
   - View model structure
   - Razor view conventions (cshtml path, `@model`, HTML patterns)
   - Localization resource usage
   - Caching patterns

4. Implement the widget, creating all required files:
   - View component class (`.cs`)
   - Properties class (`.cs`)
   - View model class (`.cs`) if needed
   - Razor view (`.cshtml`)
   - Localization resource entries (`.resx`) if applicable

5. Verify the implementation builds: `dotnet build`

6. Report which files were created and any manual steps needed (e.g., registering in Kentico admin, content type associations).
```

#### .claude/instructions/base-pagebuilder.md

```markdown
# Page Builder Widget — Rules & Conventions

## Widget Registration

- Always use `[assembly: RegisterWidget(...)]` attribute
- Widget identifier format: `CompanyName.WidgetName` (e.g., `DancingGoat.HeroWidget`)
- Icon classes come from the Kentico admin icon set

## Properties Class

- Must implement `IWidgetProperties`
- Use form component attributes for each property: `[EditingComponent(...)]`
- For linked content: use **Combined Content Selector** (`ContentItemSelectorComponent`), NOT Web Page Selector
- Always provide default values where appropriate

## Content Retrieval

- Retrieve linked content via `IContentQueryExecutor` or `XperienceCommunity.ContentRepository`
- Always check for null/empty before accessing content item properties
- Use caching via `XperienceCommunity.CacheManager` or `ICacheDependencyBuilderFactory`

## View Component

- Inherit from `ViewComponent`
- Return `ViewViewComponentResult` from `InvokeAsync`
- Inject services via constructor
- Pass a strongly-typed view model to the view — do not pass raw content items

## Razor View

- Use `@model` directive with the view model type
- Use `Html.Kentico().EditableArea()` / `Html.Kentico().Widget()` only where applicable
- Validate that view model properties are non-null before rendering
- Use `HTMLHelper.HTMLEncode()` for any user-provided string content

## Localization

- Use `IStringLocalizer<SharedResources>` (or project-equivalent) for all display strings
- Do not hardcode UI text — add entries to the appropriate `.resx` file

## General Rules

- No magic strings — use `nameof()` and constants
- Follow existing project naming and folder conventions exactly
- Do not modify generated files in `Models/Generated/`
```

#### .claude/instructions/docs.md

```markdown
# Kentico Documentation References

Use the **kentico-mcp** MCP server (`kentico_docs_search` / `kentico_docs_fetch`) to look up any of these topics before implementing.

## Core Widget Development

- Page Builder widgets overview: https://docs.kentico.com/developers-and-admins/development/builders/page-builder/page-builder-widgets
- Widget properties and form components: https://docs.kentico.com/developers-and-admins/development/builders/page-builder/page-builder-widgets/widget-properties
- Widget view components: https://docs.kentico.com/developers-and-admins/development/builders/page-builder/page-builder-widgets/widget-view-components

## Content Retrieval

- Content query API: https://docs.kentico.com/developers-and-admins/development/content-retrieval/content-query-api
- Linked content retrieval: https://docs.kentico.com/developers-and-admins/development/content-retrieval/retrieve-linked-content-items

## UI Form Components

- Built-in form components reference: https://docs.kentico.com/developers-and-admins/development/builders/form-builder/reference-built-in-form-components
- Content item selector: https://docs.kentico.com/developers-and-admins/development/builders/page-builder/page-builder-widgets/widget-properties/reference-kentico-built-in-form-components

## Page URLs & Assets

- Generating page URLs: https://docs.kentico.com/developers-and-admins/development/content-retrieval/generate-page-urls
- Media assets: https://docs.kentico.com/developers-and-admins/development/content-retrieval/media-libraries

## Caching

- Caching content: https://docs.kentico.com/developers-and-admins/development/caching
```

#### .claude/create-instructions/CREATION_TEMPLATE.md

```markdown
# Page Builder Widget Requirements

## Widget Overview

### Widget Identification

- **Widget Name**: [Widget Name]
- **Widget Identifier**: [Widget Identifier]
  - _Format: `CompanyName.WidgetName` (e.g., `DancingGoat.CardWidget`)_
- **Description**: [Brief description of the widget]
- **Icon Class**: [Icon Class]

### Purpose

[Describe the widget's purpose and use cases]

### Core Functionality

- [List the main features the widget provides]
- [Specify content selection capabilities]
- [Describe presentation templates/options]
- [List configuration options]
- [Specify linked content retrieval needs]
- [Reference existing CSS styles to use]

## Widget Properties

| Property Name | Type | Form Component | Required | Default Value | Description |
| ------------- | ---- | -------------- | -------- | ------------- | ----------- |

## Data Requirements

### External Data Sources

| Content Type Name | Property Name | Description |
| ----------------- | ------------- | ----------- |

### Data Retrieval Logic

[Describe what methods and how should they be used]

### Dependencies

[List required services or external dependencies]

## View/Presentation

### View Model Structure

| Property Name | Type | Source | Description |
| ------------- | ---- | ------ | ----------- |

### HTML Structure

[Describe the expected HTML markup]

### Styling Requirements

[CSS classes, inline styles, or custom styling]

### Responsive Behavior

[How should the widget behave on different screen sizes]

## JavaScript & Client-side Requirements

[If needed, describe any client-side interactions or behaviors with script locations]

## Registration Details

Ensure that newly created widget is registered using the RegisterWidget attribute.

## Widget File Structure

[Describe the expected file structure for the widget implementation, for each file describe how it should work/look like]

## Implementation Checklist

[Provide a checklist of implementation steps to ensure all requirements are met]

## Additional Notes

[Any additional information, constraints, or special requirements from user]
```

---

### Step 6: Update .claude/settings.local.json permissions

If `.claude/settings.local.json` exists, ensure MCP permissions are present. If it does not exist, create it:

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

If the file exists and already has a `permissions.allow` array, add the two MCP entries if they are not already there.

---

### Step 7: Report

Tell the user:
1. Which files were created
2. Whether this was detected as an existing project or a new/empty one
3. If existing: key facts discovered (Kentico version, channels, content types, notable packages)
4. If new/empty: remind them to update `CLAUDE.md` placeholders as they build out the project

**Next steps (always include):**
- Reload Claude Code to activate the MCP servers
- To design a content model before writing code: ask Claude to "start content modeling" (uses kentico-cm-mcp)

**Next steps (existing projects only):**
- To create a widget: prepare a folder with `requirements.md`, then run `/widget-create-research [folder-path]`

**Next steps (new/empty projects only):**
- Scaffold the project: `dotnet new` or follow the Kentico setup guide
- Once scaffolded, run `/init-kentico` again to regenerate `CLAUDE.md` with real project details
