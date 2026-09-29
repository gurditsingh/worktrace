# Matching Strategy

## Goal

The core intelligence of WorkTrace is deciding which Jira work item best represents a Claude session.

The matching engine should be **evidence-first, explainable, and conservative**.

The system should never jump directly to an LLM-based guess when stronger deterministic signals exist.

---

## Matching Order

Use this priority:

1. Exact Jira key
2. Git/Jira development evidence
3. User's active Jira scope
4. Semantic similarity
5. LLM reasoning
6. Human confirmation

---

# 1. Exact Jira Key

Search for Jira keys in:

- Claude conversation
- Git branch
- commit messages
- PR title
- PR description
- repository metadata

Example:

```text
branch:
DATA-1842-add-xml-reader
```

Mapping:

```text
Claude session
      ↓
Git branch
      ↓
DATA-1842
```

This should be treated as very high-confidence evidence.

---

# 2. Git and Development Evidence

Useful signals include:

- same repository
- same branch
- linked pull request
- recent commits
- files relevant to Jira component
- Jira development metadata

These signals help validate or reinforce a candidate.

---

# 3. Active Jira Scope

Before semantic matching, reduce the candidate set.

Prioritize:

```text
assignee = current user
status != done
current sprint
recently updated
same project
same repository
```

This reduces noise and makes semantic matching more reliable.

---

# 4. Semantic Similarity

Compare:

```text
Claude work summary
        ↕
Jira title + description
```

Example session:

```text
Added XML source reader and updated YAML validation.
```

Example Jira:

```text
DATA-1842 — Add XML ingestion support to ETL framework
```

This should score highly.

---

# 5. LLM Reasoning

Use an LLM only after structured evidence is available.

Example input:

```json
{
  "session": {
    "summary": "Added XML source support...",
    "repository": "etl-framework",
    "branch": "feature/xml-reader"
  },
  "candidates": [
    {
      "key": "DATA-1842",
      "summary": "Add XML ingestion support"
    },
    {
      "key": "DATA-1791",
      "summary": "Refactor readers"
    }
  ]
}
```

Expected output:

```json
{
  "best_match": "DATA-1842",
  "confidence": 0.91,
  "reason": "Session work directly implements the requested XML ingestion feature."
}
```

The model should explain its reasoning using only the supplied evidence.

---

# 6. Human Confirmation

The developer's decision is always authoritative.

```text
AI suggestion ≠ final truth
```

If the user changes the mapping, WorkTrace should persist that correction.

Later versions could optionally learn from confirmed mappings.

---

# Confidence Model

A possible scoring model:

| Evidence | Example weight |
|---|---:|
| Jira key in branch | 1.00 |
| Jira key in commit | 0.95 |
| Jira key in Claude session | 0.90 |
| Jira key in PR | 0.95 |
| Same repository | 0.20 |
| Current sprint | 0.15 |
| Same assignee | 0.10 |
| Semantic similarity | 0.00–0.80 |

These are illustrative values only.

The MVP should start simple and measure real matching behavior before optimizing weights.

---

# Explainability

Every recommendation should include evidence.

Example:

```text
Suggested Jira:
DATA-1842 — Add XML source support

Confidence:
96%

Why:
✓ Jira key found in Git branch
✓ Same repository
✓ Session summary closely matches Jira description
```

Avoid opaque output such as:

```text
DATA-1842 — 96%
```

without explanation.

---

# Missing Jira Detection

A session can be considered a possible missing Jira when:

- it represents meaningful engineering work
- no deterministic Jira key exists
- no candidate exceeds a configured confidence threshold
- the work is not classified as non-Jira activity

Example:

```text
Session:
Glue 5 compatibility debugging

Work type:
investigation + implementation

Files changed:
6

Tests:
12 passed

Best Jira match:
0.34

Result:
Potentially untracked work
```

---

# Avoiding False Positives

Not every Claude session deserves a Jira.

Possible non-Jira sessions:

- generic learning
- syntax questions
- brainstorming
- code reading with no task
- personal notes
- environment setup unrelated to a tracked deliverable

WorkTrace should classify sessions before declaring work "missing."

---

# Many-to-Many Matching

The matcher must support:

```text
multiple sessions → one Jira
one session → multiple Jira items
```

Example:

```text
Session A ──┐
            ├── DATA-101
Session B ──┘

Session C ───── DATA-102
          └──── DATA-103
```

A mapping is an entity, not just a field on the session.
