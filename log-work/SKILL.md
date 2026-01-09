---
name: log-work
description: Documents development work into changelog-style markdown files organized by date. Analyzes recent changes and creates structured work logs. Use when logging completed work or creating development history.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Overview

This skill helps you automatically document your development work into a structured markdown file, similar to a changelog. It analyzes recent code changes, git history, and creates comprehensive work logs that track all development activities.

# Usage

When invoked, this skill will:
1. Analyze recent changes in the codebase (git diff, git log)
2. Identify what features/fixes/refactors were made
3. Create or update a date-based markdown file in `docs/worklog/`
4. Include timestamps, categories, and detailed descriptions

## Default Behavior

- **File**: Creates/updates `docs/worklog/YYYY-MM-DD.md` (e.g., `docs/worklog/2026-01-09.md`)
- **Directory**: Automatically creates `docs/worklog/` if it doesn't exist
- **Format**: Entries within the same day are added to the same file (newest first)
- **Categories**: feat, fix, refactor, docs, style, test, chore, perf

## Entry Format

Each work log entry should follow this structure:

```markdown
## [HH:MM] Category: Brief Title

### Changes
- Detailed change 1
- Detailed change 2
- Detailed change 3

### Files Modified
- `path/to/file1.ext`
- `path/to/file2.ext`

### Notes
Additional context, decisions made, or future considerations.

---
```

Note: Date is not included in the entry header since it's already in the filename.

# Instructions

When the user invokes `/log-work`:

1. **Gather Context**
   - Run `git status` to see current state
   - Run `git diff` to see unstaged changes
   - Run `git log -5 --oneline` to see recent commits
   - Get current date in YYYY-MM-DD format

2. **Analyze Changes**
   - Identify the type of work (feat/fix/refactor/etc.)
   - List all modified files
   - Summarize what was changed and why
   - Note any important decisions or trade-offs

3. **Prepare File Path**
   - Create `docs/worklog/` directory if it doesn't exist
   - Determine file path: `docs/worklog/YYYY-MM-DD.md` (e.g., `docs/worklog/2026-01-09.md`)
   - Check if today's file already exists

4. **Create Entry**
   - Use current date and time for the entry header
   - Create a clear, descriptive title
   - List all changes in bullet points
   - Include file paths
   - Add any relevant notes or context

5. **Update File**
   - If today's file doesn't exist, create it with a header
   - If today's file exists, add the new entry at the TOP (reverse chronological within the day)
   - Maintain proper markdown formatting
   - Preserve existing entries from today

6. **Confirm**
   - Show the user what was added
   - Provide the file path where it was logged

For detailed examples and usage scenarios, see [EXAMPLES.md](EXAMPLES.md).

## Options

- `--date <YYYY-MM-DD>`: Use a specific date instead of today (default: today's date)
- `--category <type>`: Specify the category (feat/fix/etc.)
- `--title <text>`: Provide a custom title
- `--skip-git`: Don't analyze git changes, just create entry from user description

## Quick Examples

```bash
# Basic usage
/log-work

# With custom title
/log-work --title "Refactor authentication module"

# Specific category
/log-work --category fix --title "Fix memory leak in WebSocket handler"
```

For more examples and detailed scenarios, see [EXAMPLES.md](EXAMPLES.md).

# Important Guidelines

1. **Be Comprehensive**: Include all significant changes, not just code
2. **Be Clear**: Use clear, descriptive language that others can understand
3. **Be Consistent**: Follow the same format for all entries
4. **Be Helpful**: Add context and notes that will help future you/team
5. **Don't Duplicate**: Check existing entries to avoid redundant logs
6. **Group Related**: If multiple small changes belong together, group them in one entry

# Integration Tips

- Use this skill after completing a feature or significant work
- Pair it with `/commit` for comprehensive documentation
- Review the entry before finalizing to ensure accuracy
- Keep entries focused on one main topic per entry
- Use it daily to maintain a clear development history
- Each day gets its own file in `docs/worklog/`, making it easy to track daily progress
- You can add multiple entries to the same day's file for different tasks
- Use `--date` option to retroactively log work from previous days if needed
