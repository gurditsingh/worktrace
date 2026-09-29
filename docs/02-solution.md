# Solution

## Product Idea

WorkTrace is an **AI work reconciliation layer** between developer activity and Jira.

Its job is not simply to copy Claude conversations into Jira.

Its job is to answer:

> **What work actually happened, what Jira item should represent it, and what work is Jira currently missing?**

---

## Core Workflow

```text
Claude Code sessions
        ↓
Session discovery
        ↓
Work summarization
        ↓
Git/context enrichment
        ↓
Jira candidate retrieval
        ↓
Matching
        ↓
Human review
        ↓
Jira update / Jira creation
```

---

## Step 1 — Discover Today's Sessions

WorkTrace uses Claude Code session metadata and lifecycle hooks to identify sessions that were actually used during the day.

Useful fields include:

```text
session_id
transcript_path
cwd
session source
start time
end time
```

A resumed session should count as activity for today even if the session was originally created earlier.

---

## Step 2 — Convert the Session Into a Work Record

The raw transcript is not the final output.

WorkTrace extracts structured information such as:

```json
{
  "session_id": "abc-123",
  "repository": "etl-framework",
  "branch": "DATA-1842-add-xml-reader",
  "work_summary": "Added XML ingestion support",
  "changes": [
    "Implemented XmlReader",
    "Registered XML reader",
    "Updated YAML validation",
    "Added unit tests"
  ],
  "validation": [
    "23 tests passed"
  ],
  "files_changed": 8
}
```

The structured record becomes the input to Jira matching.

---

## Step 3 — Enrich With Git Context

Git evidence can often provide stronger signals than AI reasoning.

WorkTrace can collect:

- repository
- branch
- recent commits
- Jira keys in branch names
- Jira keys in commit messages
- changed files
- pull-request metadata when available

Example:

```text
Branch:
DATA-1842-add-xml-reader
```

That creates a deterministic mapping:

```text
Claude session
      ↓
Git branch
      ↓
DATA-1842
```

No semantic inference is needed.

---

## Step 4 — Retrieve Relevant Jira Candidates

Instead of searching every Jira issue, WorkTrace narrows the candidate set.

Initial filters can include:

- assigned to the current developer
- open items
- current sprint
- recently updated items
- same Jira project
- repository-related items
- Jira keys found in Git
- Jira keys mentioned in the Claude session

---

## Step 5 — Match Work to Jira

WorkTrace ranks candidate Jira items using multiple signals.

Example:

```text
Claude session:
Added XML reader
Updated YAML validation
Added unit tests

Candidate Jira:

94% DATA-1842 — Add XML source support
31% DATA-1791 — Refactor source readers
12% DATA-1600 — Framework improvements
```

The system should always prefer deterministic evidence before semantic inference.

See [Matching Strategy](06-matching-strategy.md).

---

## Step 6 — Reconcile

The developer reviews the recommendation.

Possible actions:

```text
[ Confirm Jira ]
[ Choose another Jira ]
[ Create Jira ]
[ Create subtask ]
[ Split across Jira items ]
[ Ignore ]
```

The system recommends; the developer remains authoritative.

---

## Step 7 — Write Back to Jira

After confirmation, WorkTrace can generate a concise implementation update.

Example:

```text
Implementation update

Implemented XML source support in the ETL framework.

Changes
• Added XmlReader implementation
• Registered XML reader
• Extended YAML mapping and validation
• Added unit-test coverage

Validation
• 23 tests passed

Evidence
• Repository: etl-framework
• Branch: DATA-1842-add-xml-reader
• Claude session: abc-123
```

This is more useful than either:

```text
Fixed XML support.
```

or dumping an entire Claude transcript into Jira.

---

## Key Product Principle

### Reconcile, don't archive

WorkTrace is not primarily a transcript backup system.

The goal is:

```text
raw activity
    ↓
understand work
    ↓
map to system of record
    ↓
preserve useful evidence
```

---

## Human-in-the-Loop

Automatic Jira creation and updates can create noise if done incorrectly.

The first versions of WorkTrace should therefore use this model:

```text
AI proposes
    ↓
developer confirms
    ↓
WorkTrace writes
```

Human confirmation is especially important for:

- uncertain mappings
- one session spanning multiple tasks
- research sessions
- work that should become a subtask rather than a story
- sessions that should not be represented in Jira at all

---

## Many-to-Many Mapping

WorkTrace should not assume:

```text
1 Claude session = 1 Jira
```

Real development can look like:

```text
Session A ─────→ DATA-101
Session B ─────→ DATA-101

Session C ─────→ DATA-102
         └─────→ DATA-103

Session D ─────→ No Jira / research
```

The underlying model must support many-to-many relationships.

---

## Product Promise

> **At the end of the day, reconcile the work you actually did with the work Jira says you did.**
