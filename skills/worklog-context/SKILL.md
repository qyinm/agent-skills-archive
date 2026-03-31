---
name: worklog-context
description: Analyzes past work history from worklogs to provide relevant context for current development tasks. Searches and summarizes previous work automatically. Use when starting new features, debugging issues, or understanding past decisions.
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
---

# Overview

This skill automatically analyzes work history from `docs/worklog/` files to provide relevant context for your current development work. It reads past work logs, identifies patterns, related changes, and provides a summary that helps inform current development decisions.

# Purpose

When starting new features or debugging issues, having context about:
- What was previously implemented and how
- Which files were modified for similar features
- Past decisions and their reasoning
- Related bugs and fixes
- Architecture patterns used

This skill provides that context automatically by analyzing your worklog history.

# Usage

When invoked, this skill will:
1. Scan `docs/worklog/` directory for existing work logs
2. Analyze entries based on search criteria (topic, files, timeframe)
3. Summarize relevant past work
4. Identify related files and patterns
5. Provide actionable context for current development

## Default Behavior

- **Search Range**: Last 30 days of worklogs
- **Output**: Structured summary with key findings
- **Focus**: Most relevant entries to current task

# Instructions

When the user invokes `/worklog-context`:

## 1. **Check Worklog Availability**
   - Use Glob to find all files in `docs/worklog/`
   - If no worklogs exist, inform user and suggest using `/log-work` first
   - Sort files by date (newest first)

## 2. **Determine Search Criteria**

   Based on user input, identify:
   - **Topic/Keyword**: Specific feature, component, or technology (e.g., "authentication", "database", "API")
   - **Timeframe**: How many days back to search (default: 30 days)
   - **File-based**: If user mentions specific files, find work related to those files
   - **Category**: Filter by work type (feat, fix, refactor, etc.)

## 3. **Read and Analyze Worklogs**

   - Read relevant worklog files (start with most recent)
   - Extract entries that match search criteria:
     - Search in titles for keywords
     - Search in Changes section for relevant work
     - Search in Files Modified for specific file paths
     - Check Notes section for important context

## 4. **Synthesize Information**

   Create a structured summary containing:

   **A. Overview**
   - How many relevant entries found
   - Date range covered
   - Main themes/patterns

   **B. Key Past Work**
   - List relevant entries with dates and summaries
   - Highlight important decisions or approaches
   - Note any breaking changes or major refactors

   **C. Frequently Modified Files**
   - Files that appear multiple times in related work
   - Suggests these are key files for current task

   **D. Patterns and Insights**
   - Common approaches used
   - Technologies/libraries chosen
   - Architectural decisions
   - Known issues or limitations

   **E. Recommendations**
   - Based on past work, suggest approach for current task
   - Warn about potential pitfalls mentioned in past work
   - Suggest files to examine based on similar past work

## 5. **Present Context**

   Format the output clearly:
   ```markdown
   # Worklog Context Analysis

   **Search Criteria**: [topic/files/category]
   **Date Range**: [start] to [end]
   **Entries Found**: [count]

   ---

   ## Key Past Work

   ### [Date] - [Title]
   - Summary of what was done
   - Key decisions made
   - Files modified: `file1.ts`, `file2.ts`

   ---

   ## Frequently Modified Files

   1. `src/auth/auth.service.ts` (modified 5 times)
   2. `src/config/database.ts` (modified 3 times)

   ---

   ## Patterns & Insights

   - JWT used consistently for authentication
   - Database connections use pooling with retry logic
   - CORS configured per environment

   ---

   ## Recommendations for Current Work

   1. Review `src/auth/auth.service.ts` - core authentication logic
   2. Follow established JWT pattern from 2026-01-09
   3. Be aware: CORS issues fixed on 2026-01-08, check current config
   ```

## Options

- `--topic <keyword>`: Search for specific topic or keyword
- `--days <N>`: Limit search to last N days (default: 30)
- `--files <pattern>`: Find work related to specific files or file patterns
- `--category <type>`: Filter by category (feat, fix, refactor, etc.)
- `--recent`: Quick summary of last 7 days (shorthand for --days 7)

## Example Usage

```bash
# Find all past work related to authentication
/worklog-context --topic authentication

# See what was done with a specific file
/worklog-context --files src/auth/auth.service.ts

# Get context for database-related work from last 60 days
/worklog-context --topic database --days 60

# Quick summary of recent work
/worklog-context --recent

# Find all bug fixes from past month
/worklog-context --category fix --days 30

# Multiple keywords (searches for either)
/worklog-context --topic "auth OR login OR jwt"

# Just ask naturally - Claude will understand
"Show me what we've done with the API in the past 2 weeks"
```

# Advanced Usage

## Automatic Context Before Development

When user says something like:
- "I need to add a new authentication method"
- "Fix the database connection issue"
- "Refactor the API endpoints"

**Proactively offer to search worklogs first**:
- "I can check our worklog history for past authentication work. Would you like me to run `/worklog-context --topic authentication` first?"

## Integration with Other Skills

This skill pairs well with:
- **Before `/log-work`**: Review past work to ensure new entry doesn't duplicate
- **Before development**: Get context on what was tried before
- **During debugging**: See if similar issues were fixed before
- **Code review**: Understand why certain patterns were used

# Important Guidelines

1. **Be Selective**: Don't overwhelm with every entry - focus on most relevant
2. **Highlight Decisions**: Surface important architectural or technical decisions
3. **Show Patterns**: If similar work was done multiple times, note the pattern
4. **Time-Aware**: More recent entries are usually more relevant
5. **File-Focused**: Files that appear frequently are likely important
6. **Actionable**: Always end with concrete recommendations for current work
7. **Context, Not Code**: Summarize decisions and approaches, not detailed code
8. **Flag Issues**: If past work notes problems or limitations, highlight them

For detailed usage scenarios and examples, see [SCENARIOS.md](SCENARIOS.md).

# Output Format

Always structure the output as:

1. **Quick Summary** (2-3 sentences)
2. **Relevant Entries** (chronological, most recent first)
3. **Key Files** (files that matter for this topic)
4. **Insights** (patterns, decisions, gotchas)
5. **Next Steps** (recommendations based on history)

# Edge Cases

- **No worklogs found**: Suggest user start using `/log-work`
- **No relevant entries**: Expand search criteria or suggest broader terms
- **Too many results**: Summarize by category or time period
- **Ambiguous query**: Ask user to clarify topic or provide more specific keywords
