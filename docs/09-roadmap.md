# Roadmap

## Guiding Principle

Build the reconciliation engine before the polished UI.

The roadmap should validate the riskiest assumptions first.

---

# Phase 0 — Concept Validation

Goal:

Confirm the workflow is useful before building significant infrastructure.

Tasks:

- validate Claude session discovery
- inspect transcript structure
- identify useful summary fields
- test Jira retrieval
- test deterministic matching
- manually evaluate semantic matching quality

Deliverable:

```text
A script that prints today's sessions and likely Jira mappings
```

---

# V0 — Read-Only CLI MVP

Goal:

Prove end-of-day reconciliation.

Capabilities:

- capture Claude sessions
- store session metadata in SQLite
- summarize meaningful sessions
- collect Git context
- retrieve Jira candidates
- rank mappings
- flag likely missing Jira work
- display results in a CLI
- persist user-confirmed mappings

No automatic Jira writes.

Example:

```text
$ jira-reconcile

3 engineering sessions found
2 Jira items matched
1 Jira item potentially missing
```

---

# V0.5 — Controlled Jira Write-Back

Add:

- generate Jira-ready implementation comment
- preview before writing
- confirm before writing
- add Jira comment
- optionally create a Jira item after confirmation

Still local-first.

---

# V1 — Reconciliation UI

Build the two-panel interface.

```text
┌─────────────────────────────┬──────────────────────────────┐
│ JIRA                        │ CLAUDE SESSIONS              │
│                             │                              │
│ DATA-101                    │ Session A                    │
│ DATA-102                    │ Session B                    │
│ DATA-103                    │ Session C                    │
└─────────────────────────────┴──────────────────────────────┘
```

Add:

- automatic suggested links
- confidence indicators
- drag-and-drop correction
- session detail panel
- create-Jira action
- ignore action
- split/merge mappings

---

# V2 — Multi-Agent Support

Generalize from Claude Code sessions to agent activity.

Potential sources:

- Claude Code
- Codex
- Cursor
- GitHub Copilot agents
- Gemini CLI
- other coding agents

The abstraction becomes:

```text
Agent Session
     ↓
Work Evidence
     ↓
WorkTrace
     ↓
System of Record
```

---

# V3 — Multiple Work Management Systems

Potential destinations:

- Jira
- Linear
- Azure DevOps
- GitHub Issues
- other systems of record

WorkTrace should not permanently couple its internal model to Jira-specific concepts.

---

# V4 — Team and Analytics Features

Potential future capabilities:

- team reconciliation dashboard
- untracked-work trends
- agent-assisted delivery analytics
- mapping quality metrics
- work evidence history
- sprint reconciliation
- manager visibility

These features should not be built until the single-developer workflow proves valuable.

---

# Metrics to Track

Useful product metrics:

```text
sessions discovered
meaningful sessions classified
deterministic matches
semantic matches
manual corrections
missing Jira items identified
Jira items created
time to reconcile a day
false positive rate
false negative rate
```

The most important early metric is:

> **How often does the developer accept the first suggested Jira match?**
