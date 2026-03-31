---
name: career-management
description: Organizes and maintains a filesystem-based career management workspace including resume versioning, job application pipelines, company research reuse, and progress tracking. Use when creating/updating resumes, adding applications, preparing interviews, or cleaning career docs structure.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Overview

This skill standardizes career documents in a single workspace:
- resume versioning (`resume/verN/`)
- job application tracking (`job-applications/{year}/{company}-{position}/`)
- reusable company research (`job-applications/companies/`)
- progress tracking (`job-applications/status.md`)

Use this skill when you need to create, reorganize, or update career artifacts while preserving consistent naming and folder conventions.

# Core Conventions

## 1) Resume Versioning

- Store each version in `resume/verN/`.
- Keep file names aligned with folder version: `verN.md`, `verN.typ`, `verN.pdf`.
- Prefer keeping source (`.md`/`.typ`) and output (`.pdf`) together in the same version folder.

### New Resume Version

```bash
mkdir -p resume/ver5
# add ver5.typ, ver5.md (optional), ver5.pdf
```

## 2) Job Application Structure

Use year + company-position convention:

```text
job-applications/{year}/{company}-{position}/
├── 00_job_description/
├── 01_application/
├── 02_interview-prep/
└── 03_progress/
```

Folder name must be kebab-case: `{company}-{position}`.

### New Application Skeleton

```bash
mkdir -p job-applications/{year}/{company}-{position}/{00_job_description,01_application,02_interview-prep,03_progress}
```

## 3) Reusable Company Research

- Keep company research separate from application instances:
  - `job-applications/companies/{company-name}/company-research.md`
- Reuse this across multiple applications to avoid duplication.

## 4) Progress Tracking

- Update `job-applications/status.md` whenever application state changes.
- Keep status entries short, date-stamped, and actionable.

# Execution Workflow

When invoked:

1. Detect root structure (`resume/`, `job-applications/`, `CLAUDE.md`).
2. Normalize folders to the conventions above.
3. Apply requested change (new resume version, new application, interview prep updates, etc.).
4. Preserve existing content; avoid destructive moves unless explicitly requested.
5. Provide a concise summary with changed paths.

# Output Rules

- Write documents in Korean by default; keep technical terms in English when clearer.
- Use concise, scan-friendly markdown sections.
- If generating resume content, prioritize measurable outcomes (metrics, impact, scope).

# Safety Checks

Before finishing:

1. Confirm naming consistency (`verN`, kebab-case company-position).
2. Confirm required subfolders exist for each new application.
3. Confirm no unrelated files were modified.
