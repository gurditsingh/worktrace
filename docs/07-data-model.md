# Data Model

## Design Principle

WorkTrace should model Claude sessions and Jira work items independently, with an explicit many-to-many mapping between them.

Do not model:

```text
ClaudeSession.jira_key
```

as the only relationship.

A single session may involve multiple Jira items, and one Jira item may be worked on across multiple sessions.

---

# ClaudeSession

Suggested fields:

```text
session_id
transcript_path
cwd
repository
branch
start_time
end_time
source
summary
work_type
files_changed
validation_summary
created_at
updated_at
```

Example:

```json
{
  "session_id": "abc-123",
  "transcript_path": "/Users/me/.claude/.../abc-123.jsonl",
  "cwd": "/projects/etl-framework",
  "repository": "etl-framework",
  "branch": "DATA-1842-add-xml-reader",
  "start_time": "2026-09-28T09:15:00",
  "end_time": "2026-09-28T10:42:00",
  "source": "resume",
  "summary": "Added XML ingestion support",
  "work_type": "implementation",
  "files_changed": 8,
  "validation_summary": "23 tests passed"
}
```

---

# JiraWorkItem

Suggested fields:

```text
jira_key
summary
description
status
assignee
sprint
project
repository
updated_at
```

Example:

```json
{
  "jira_key": "DATA-1842",
  "summary": "Add XML source support",
  "description": "Support XML sources in the generic ETL reader framework.",
  "status": "In Progress",
  "assignee": "developer",
  "sprint": "Sprint 14",
  "project": "DATA"
}
```

---

# SessionJiraMapping

Suggested fields:

```text
id
session_id
jira_key
confidence
mapping_type
evidence
user_confirmed
created_at
updated_at
```

Example:

```json
{
  "session_id": "abc-123",
  "jira_key": "DATA-1842",
  "confidence": 0.96,
  "mapping_type": "branch_key",
  "evidence": [
    "Jira key found in Git branch",
    "Semantic summary match"
  ],
  "user_confirmed": true
}
```

---

# Mapping Types

Possible values:

```text
branch_key
commit_key
session_key
pr_key
semantic
manual
created_new
```

This makes it easier to understand how a mapping was produced.

---

# Example Relationship

```text
ClaudeSession
────────────────
abc-123
abc-456
abc-789


SessionJiraMapping
─────────────────────────────
abc-123 → DATA-1842
abc-456 → DATA-1842
abc-789 → DATA-1933
abc-789 → DATA-1950


JiraWorkItem
────────────────
DATA-1842
DATA-1933
DATA-1950
```

---

# Optional Future Entities

## DailyReconciliation

Could represent one review session:

```text
id
date
user
started_at
completed_at
sessions_reviewed
mappings_confirmed
missing_items_found
```

---

## WorkEvidence

Could separate evidence from mapping:

```text
id
session_id
type
value
source
confidence
```

Example evidence types:

```text
git_branch
git_commit
pr
file_change
test_result
explicit_jira_key
semantic_similarity
```

This becomes useful if WorkTrace evolves into a generalized evidence graph.

---

# SQLite for MVP

SQLite is sufficient for the first version because the MVP is:

- single-user
- local-first
- low-volume
- easy to inspect
- easy to migrate later

The schema should remain simple enough to understand manually during early development.
