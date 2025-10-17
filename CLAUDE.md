# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **Claude Code plugin marketplace repository** containing reusable plugins for enhanced development workflows. The repository follows Claude Code's plugin marketplace specification with a dual-manifest structure.

## Architecture

### Plugin Marketplace Structure

```
.claude-plugin/
├── marketplace.json     # Marketplace catalog (lists all available plugins)
└── plugin.json          # Individual plugin manifest (describes smart-commit)
```

**Key distinction:**
- `marketplace.json` - Defines the marketplace and catalogs available plugins with their source locations
- `plugin.json` - Contains metadata for the individual plugin (currently smart-commit)

### Manifest Relationship

The `marketplace.json` references plugins via the `source` field:
- `"source": "./"` - Plugin at repository root (current setup)
- `"source": "./plugins/name"` - Plugin in subdirectory (for multi-plugin repos)

The marketplace manifest **must** include:
- `name` - Marketplace identifier (kebab-case)
- `owner.name` - Maintainer information
- `plugins[]` - Array of plugin entries with `source` starting with `./`

### Skills Architecture

Skills use **progressive disclosure** to minimize context pollution:
1. **Metadata** (YAML frontmatter) - Always loaded, contains name and description
2. **Instructions** (markdown content) - Loaded when skill is invoked
3. **Resources/examples** - Embedded in instruction sections

Skills are auto-invoked by Claude based on context matching (e.g., "create a commit" → smart-commit skill).

## Plugin Development Workflow

### Testing Locally

Install from local path before pushing:

```bash
/plugin install /home/kahl/repos/claude-code-plugins
```

Or add as marketplace:

```bash
/plugin marketplace add /home/kahl/repos/claude-code-plugins
/plugin install smart-commit
```

### Adding New Plugins

**For single plugin at root (current):**
1. Add plugin components to root directories (`skills/`, `commands/`, `agents/`, `hooks/`)
2. Update `plugin.json` with new metadata
3. Keep `marketplace.json` source as `"./"`

**For multiple plugins (future):**
1. Create `plugins/<plugin-name>/` subdirectory
2. Move plugin components into subdirectory
3. Add new entry to `marketplace.json` plugins array:
   ```json
   {
     "name": "new-plugin",
     "source": "./plugins/new-plugin",
     "description": "...",
     "version": "1.0.0"
   }
   ```
4. Create `plugins/<plugin-name>/.claude-plugin/plugin.json`

### Version Management

Follow semantic versioning in both `plugin.json` and `marketplace.json`:
- **MAJOR**: Breaking changes to skill behavior or API
- **MINOR**: New features (new skills, commands, or capabilities)
- **PATCH**: Bug fixes, documentation updates

Update versions in **both files** when releasing.

## Schema Validation

The marketplace.json is strictly validated by Claude Code:

**Required fields:**
- `name`: string (kebab-case)
- `owner.name`: string
- `plugins`: array with entries containing:
  - `name`: string
  - `source`: string (must start with `./`)
  - `description`: string
  - `version`: string (semver)

**Common validation errors:**
- `source` must start with `./` (not `.` or absolute paths)
- Plugin names should be kebab-case
- Version must follow semver format

## Commit Conventions

This repository uses **conventional commits** (standard mode, no Jira tickets):

```
<type>: <description>

Types: feat, fix, docs, refactor, test, chore
```

The smart-commit skill auto-detects this by analyzing git history.
