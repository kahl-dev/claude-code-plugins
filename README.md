# Claude Code Plugins

Public collection of Claude Code plugins for enhanced development workflows.

## 📦 Plugins

### Smart Commit

Intelligent git commit workflow with auto-detection for Jira vs conventional commit formats.

**Features:**
- 🔍 Auto-detects project commit conventions from git history
- 🎫 Jira ticket integration with smart extraction from branch names
- ✅ Conventional commit format support
- 🎯 Context-aware commit message formatting
- 🔄 Pre-commit hook handling with automatic retries

**Usage:**

The skill activates automatically when you ask Claude to create commits:

```
"create a commit for these changes"
"commit this code"
"make a commit with message: add authentication"
```

**How it works:**

1. Analyzes last 20 commits to detect if project uses Jira tickets
2. If >50% have Jira tickets → uses `feat(PROJ-123): description` format
3. If <50% have Jira tickets → uses `feat: description` format
4. Extracts Jira tickets from branch names automatically
5. Validates commit format and handles pre-commit hooks

## 🚀 Installation

### Install from GitHub

Add the plugin marketplace and install:

```
/plugin marketplace add kahl-dev/claude-code-plugins
/plugin install smart-commit
```

### Local Development (Optional)

Clone the repository for local development or testing:

```bash
git clone https://github.com/kahl-dev/claude-code-plugins.git
cd claude-code-plugins
/plugin install .
```

## 📁 Repository Structure

```
claude-code-plugins/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── skills/
│   └── smart-commit/        # Smart commit skill
│       └── SKILL.md
└── README.md
```

## 🛠️ Development

This is a single-plugin repository. Future plugins may be added as the collection grows.

### Plugin Format

Skills use Claude Code's skill format:
- YAML frontmatter with name and description
- Markdown content with progressive disclosure
- Automatic invocation based on context

## 📝 License

MIT License - See LICENSE file for details

## 🤝 Contributing

Contributions welcome! Feel free to:
- Report issues
- Suggest improvements
- Submit pull requests

## 🔗 Links

- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Development Guide](https://docs.claude.com/en/docs/claude-code/plugins)
