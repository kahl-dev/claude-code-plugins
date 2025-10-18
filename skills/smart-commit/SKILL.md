---
name: smart-commit
description: Workflow to commit always the right way.
---

# Smart Commit

## Overview

Smart Commit provides an intelligent git commit workflow that automatically adapts to project conventions by analyzing commit history. It determines whether to use Jira-integrated commits (`feat(PROJ-123): description`) or conventional commits (`feat: description`) based on the project's existing patterns.

## Workflow Decision Tree

When asked to create a commit, follow this workflow:

1. **Detect commit format** → Check recent commit history
2. **Gather information** → Extract necessary details (ticket ID if needed)
3. **Review changes** → Run git status, diff, and log
4. **Create commit** → Format message and execute
5. **Verify success** → Check status and handle hooks

## Phase 1: Context Detection & Analysis

### Auto-Detection Logic

Before creating any commit, analyze the project's commit history:

```bash
git log --oneline -20 --pretty=format:"%s"
```

**Analysis rules:**

- Count commits matching Jira pattern: `[A-Z]+-[0-9]+`
- If >50% have Jira tickets → **Jira Mode**
- If <50% have Jira tickets → **Standard Mode**

### User Override Options

Accept optional mode arguments to override auto-detection:

- `jira` - Force Jira mode regardless of history
- `no-jira` - Force standard mode regardless of history
- `PROJ-123` format - Use specific Jira ticket in commit

### Jira Ticket Extraction (Jira Mode Only)

When Jira mode is active, extract ticket ID using this priority:

1. **Explicit ticket** - User provided `PROJ-123` format → use directly
2. **Branch name** - Extract from patterns:
   - `feature/LIA-123-description` → `LIA-123`
   - `fix/PROJ-456-bug-name` → `PROJ-456`
   - `LIA-789-hotfix` → `LIA-789`
   - `bugfix/TASK-101-issue` → `TASK-101`
3. **Last commit** - Ask: "Last commit used LIA-456. Reuse? (y/N)"
4. **Manual input** - Request ticket ID from user

**Ticket validation:**

- Must match pattern: `[A-Z]+-[0-9]+`
- Examples: `LIA-123`, `PROJ-456`, `TASK-789`
- Invalid: `lia-123`, `PROJ123`, `123-PROJ`

## Phase 2: Standard Git Workflow

### Pre-Commit Analysis

Always execute these commands in parallel before creating commit:

```bash
git status                    # Check current state
git diff                      # Review modifications
git log --oneline -5          # Recent commit context
```

### File Staging

Stage relevant files based on changes:

- Stage modified files shown in git status
- Stage new files if appropriate
- Exclude files that shouldn't be committed (secrets, temp files)
- Ask user if uncertain about staging decisions

## Phase 3: Commit Message Format

### Jira Mode Format

```
<type>(<TICKET-ID>): <description>
```

**Example:**

```bash
git commit -m "feat(LIA-123): add user authentication"
```

### Standard Mode Format

```
<type>[optional scope]: <description>
```

**Example:**

```bash
git commit -m "feat: add user authentication"
```

### Commit Types (Both Modes)

- **feat** - New feature (MINOR semantic version)
- **fix** - Bug fix (PATCH semantic version)
- **docs** - Documentation changes
- **style** - Formatting changes (no code logic changes)
- **refactor** - Code refactoring (no feature change)
- **perf** - Performance improvements
- **test** - Adding or updating tests
- **chore** - Build, dependencies, tooling changes

### Message Construction Rules

Apply these rules for both modes:

- Type is mandatory
- Description is mandatory (lowercase, no period at end)
- Use present tense ("add" not "added")
- Be descriptive but concise
- Focus on WHAT and WHY, not HOW
- Start description with lowercase
- Use present tense verbs

**Validation checklist:**

- ✅ No periods at the end
- ✅ Starts with lowercase
- ✅ Concise but descriptive
- ✅ Present tense verbs

## Phase 4: Commit Execution

### Create Commit with HEREDOC

Always use HEREDOC formatting for commit messages:

```bash
git commit -m "$(cat <<'EOF'
feat(LIA-123): add user authentication
EOF
)"
```

### Verify Success

After committing, verify with:

```bash
git status
```

### Handle Pre-Commit Hooks

If hooks modify files:

1. Check authorship: `git log -1 --format='%an %ae'`
2. Check not pushed: Verify branch is ahead
3. If both true: Amend commit to include hook changes
4. Otherwise: Create new commit (never amend others' commits)

**Important:**

- Never use `--no-verify` to skip hooks
- Auto-stage hook modifications and retry
- Amend only when safe (own commit, not pushed)

## Phase 5: Error Handling

### Common Error Scenarios

**No changes to commit:**

- Check git status first
- Inform user: "No changes to commit"
- Exit gracefully

**Invalid ticket format:**

- Validate against `[A-Z]+-[0-9]+` pattern
- Ask for correction if invalid
- Show example: "Expected format: PROJ-123"

**Commit fails:**

- Show exact error message
- Suggest fixes (stage files, resolve conflicts)
- Retry with corrected approach

**Branch extraction fails:**

- Fall back to checking last commit
- Offer manual input
- Allow switching to standard mode

### Recovery Actions

When commit fails:

1. Display the exact error
2. Analyze the cause
3. Suggest specific fix
4. Ask user if they want to retry
5. Execute corrected commit

## Usage Examples

### Auto-Detection Examples

**Scenario 1: Work project with Jira**

```
git log shows: "feat(LIA-123): add auth", "fix(LIA-124): resolve bug"
→ Auto-detects Jira mode
→ Extracts ticket from branch or asks user
→ Creates: feat(LIA-125): add dashboard
```

**Scenario 2: Personal project**

```
git log shows: "feat: add auth", "fix: resolve login bug"
→ Auto-detects standard mode
→ Creates: feat: add dashboard
```

**Scenario 3: Mixed project with override**

```
User: "commit this with no-jira"
→ Forces standard mode regardless of history
→ Creates: feat: update readme
```

### User Interaction Examples

**When ticket needed but not found:**

```
"I need a Jira ticket ID for this commit.
The last commit used LIA-456. Use this ticket? (y/N)"
```

**When branch has ticket:**

```
"Extracted LIA-123 from branch name 'feature/LIA-123-oauth'.
Use this ticket? (y/N)"
```

**When multiple files changed:**

```
"I see changes to auth.ts, login.ts, and types.ts.
Should I stage all three files? (Y/n)"
```

## Success Criteria

A successful commit execution requires:

- ✅ Correct format applied based on project context
- ✅ Valid ticket ID when in Jira mode
- ✅ Proper staging of relevant files only
- ✅ Clean execution with error handling
- ✅ Hook compliance with automatic retries
- ✅ Verification via git status after commit
