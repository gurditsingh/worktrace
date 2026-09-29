# MVP

## MVP Goal

The MVP should prove one thing:

> **Can WorkTrace reliably discover today's Claude Code work, propose useful Jira matches, and identify missing Jira coverage?**

The most important early risk is **matching quality**, not UI design.

Therefore the first version should avoid building a polished two-panel application.

---

# V0 — Read-Only Reconciliation

The first proof of concept should be local and read-only.

Suggested interface:

```text
jira-reconcile
```

or, later, a Claude skill/command such as:

```text
/jira-reconcile
```

---

## MVP Component 1 — Session Collector

Use Claude Code lifecycle hooks to record sessions.

Initial events:

```text
SessionStart
SessionEnd
```

Store:

```text
session_id
transcript_path
cwd
repository
branch
start_time
end_time
source
```

Suggested local storage:

```text
SQLite
```

### Requirement

A session resumed today must appear in today's reconciliation even if it was originally created on a previous day.

---

## MVP Component 2 — Transcript Summarizer

Read the selected Claude transcript and generate a structured work summary.

Suggested fields:

```text
objective
summary
changes
investigation
validation
outcome
components affected
```

The summarizer should not treat every conversation as Jira work.

Examples that may be ignored:

- casual questions
- learning/research unrelated to delivery
- trivial command help
- sessions with no meaningful engineering output

---

## MVP Component 3 — Git Enrichment

Collect:

```text
repository
branch
changed files
recent commits
Jira key in branch
Jira key in commits
pull request metadata if available
```

Git evidence should be evaluated before using an LLM.

---

## MVP Component 4 — Jira Candidate Retrieval

Read relevant Jira items.

The initial search scope should be intentionally narrow:

```text
assigned to current user
open items
current sprint
recently updated
same Jira project
repository-related work
explicit Jira IDs found in context
```

Integration options:

- Atlassian Rovo MCP
- Jira REST API
- another supported Jira connector

The first implementation only needs one.

---

## MVP Component 5 — Matching Engine

Use two stages:

### Stage 1 — Deterministic

Examples:

```text
Jira key in branch
Jira key in commit
Jira key in Claude transcript
Jira key in PR title
```

### Stage 2 — Semantic

When deterministic evidence is absent:

```text
session work summary
        ↕
Jira title + description
```

Return:

```text
candidate Jira
confidence
evidence
```

---

## MVP Component 6 — Reconciliation CLI

Example:

```text
$ jira-reconcile

TODAY — Sep 28
──────────────────────────────────────────────────

1. XML Reader
   09:15–10:42

   Suggested:
   DATA-1842 — Add XML source support

   Confidence: 96%
   Evidence:
   • Jira key found in Git branch

   [confirm] [choose] [ignore]


2. Glue 5 Compatibility
   11:17–12:06

   No confirmed Jira found.

   Suggested new Jira:
   "Validate ETL Framework on AWS Glue 5"

   [create] [link] [ignore]


3. GDPR Purge Fix
   14:04–15:21

   Suggested:
   DATA-1933 — Update GDPR purge process

   Confidence: 87%

   [confirm] [choose] [ignore]
```

---

## MVP Component 7 — Explicit User Decisions

For each session:

```text
Confirm existing Jira
Choose another Jira
Create Jira
Create subtask
Split work
Ignore
```

For the earliest POC, "create" can simply print the proposed Jira payload rather than writing it.

---

## MVP Component 8 — Jira Write-Back

Enable this only after matching quality is proven.

First write capability:

```text
Add implementation summary as Jira comment
```

Then later:

```text
Create Jira
Create subtask
Update description
Transition status
```

All write operations should initially require explicit confirmation.

---

# MVP User Flow

```text
Developer works normally
        ↓
Hooks register Claude sessions
        ↓
Developer runs jira-reconcile
        ↓
WorkTrace finds today's sessions
        ↓
Meaningful sessions are summarized
        ↓
Git context is added
        ↓
Relevant Jira items are retrieved
        ↓
Mappings are proposed
        ↓
Developer confirms/corrects
        ↓
Potential missing Jira work is highlighted
```

---

# Out of Scope for V0

Do not include:

- enterprise manager dashboards
- organization-wide analytics
- billing/time attribution
- automatic Jira creation
- automatic Jira status transitions
- full transcript synchronization
- support for every coding agent
- team sharing
- complex RBAC
- polished drag-and-drop UI
- mobile app
- sprint analytics

The V0 focus is:

```text
Find sessions
      ↓
Understand work
      ↓
Match Jira
      ↓
Detect missing Jira
      ↓
User confirms
```

---

# Success Criteria

The MVP is successful when:

1. **Session discovery works**  
   Today's used/resumed Claude sessions are reliably found.

2. **Deterministic mapping works**  
   Jira keys in Git/session context produce correct mappings without unnecessary AI calls.

3. **Candidate suggestions are useful**  
   For unmatched sessions, the developer usually confirms one of the suggested Jira items instead of manually searching.

4. **Missing work is visible**  
   Meaningful sessions with no Jira coverage are clearly identified.

5. **Generated summaries are useful**  
   Jira-ready updates are materially better than retrospective one-line notes.

6. **Reconciliation is fast**  
   A full workday can be reviewed in a few minutes.

---

# MVP Exit Criteria

Move to the full UI only after:

- session collection is reliable
- summaries are consistently useful
- deterministic matching is stable
- semantic matching quality is acceptable
- users can understand why a Jira was suggested
- false-positive "missing Jira" detection is manageable

The UI should productize a proven reconciliation engine rather than hide an unreliable one.
