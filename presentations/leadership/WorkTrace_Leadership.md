# WorkTrace — Leadership Deck

> **AI Work Reconciliation for Developers**

This document is the version-controlled content source for the WorkTrace technical-leadership presentation.

---

# Slide 1 — WorkTrace

## AI Work Reconciliation for Developers

**Reconcile the work developers actually did in AI coding sessions with the work Jira says they did.**

Concept / MVP design · Leadership overview

---

# Slide 2 — Where Engineering Work Happens Has Shifted

## AI coding agents are now a primary development surface — but our tracking assumes the old trail

### Before coding agents

```text
Ticket
  ↓
Branch
  ↓
Commits
  ↓
Pull request
```

**Work trail visible end-to-end**

### With coding agents

```text
Problem exploration
       ↓
Agent conversation + tool calls
       ↓
Code changes + validation
       ↓
Follow-up changes
```

**A significant part of the engineering trail now lives inside the agent session.**

---

# Slide 3 — The Gap: Work That Happened vs. What Jira Says

## A typical day for a developer working in Claude Code

| Time | Actual work | Jira state |
|---|---|---|
| 09:00–10:30 | Add XML reader to ETL framework | No Jira created |
| 11:00–12:00 | Debug Glue 5 compatibility | Maybe related to a migration story |
| 14:00–15:30 | GDPR purge fix | Jira exists, no implementation update |
| 17:00 | Developer remembers tracking | One broad ticket: "Fixed pipeline issue" |

### What gets lost

- Root-cause analysis
- Alternatives considered
- Files and components changed
- Tests run and results
- Implementation decisions

**Evidence is scattered across transcripts, branches, commits, PRs, and test output — with no single step asking:**

> **Is all of today's work represented in Jira?**

---

# Slide 4 — Why It Matters

## Jira is our system of record — when it drifts from reality, so does everything built on it

### 1. Effort is under-represented

Investigation and debugging work can disappear from the record.

### 2. Reporting gets less accurate

Sprint and delivery reporting can reflect broad after-the-fact tickets rather than the actual work performed.

### 3. Traceability breaks

The link between request, decision, implementation, and validation becomes difficult to reconstruct.

### 4. AI impact stays invisible

We cannot clearly see how agent-assisted engineering work contributes to delivery.

---

# Slide 5 — The Idea: An AI Work Reconciliation Layer

## Treat AI coding sessions as evidence of engineering work — and reconcile that evidence against Jira

```text
1. Discover
   Find today's sessions, including resumed sessions

2. Summarize
   Turn transcripts into structured work records

3. Enrich
   Add Git branch, commits, PRs, and changed files

4. Match
   Rank likely Jira items with confidence and evidence

5. Confirm
   Developer approves; Jira is updated only after confirmation
```

> **At the end of the day, reconcile the work you actually did with the work Jira says you did.**

### Key principle

**Reconcile, don't archive.**

The goal is an accurate system of record, not transcript storage.

---

# Slide 6 — What the Developer Sees

## A few minutes at the end of the day: review and confirm instead of reconstructing from memory

```text
$ jira-reconcile

✓ XML reader implementation
  DATA-1842 · Add XML source support
  96% · Jira key found in Git branch

✓ GDPR purge correction
  DATA-1933 · Update GDPR purge process
  87% · semantic match + same sprint

⚠ Glue 5 compatibility investigation
  No Jira found
  → Suggest: "Validate ETL on Glue 5"

○ MCP research
  → Ignore / research
```

### Every suggestion remains a choice

- Confirm Jira
- Choose another Jira
- Create Jira / subtask
- Split across items
- Ignore

```text
AI proposes
     ↓
Developer confirms
     ↓
WorkTrace writes
```

**The developer remains authoritative.**

---

# Slide 7 — How Matching Works: Evidence Before Inference

## Deterministic signals first; AI only when needed; human always has the final say

| Priority | Signal | Example |
|---:|---|---|
| 1 | Exact Jira key | Branch, commit, PR, or session text |
| 2 | Git / development evidence | Same repo, branch, linked PR, changed files |
| 3 | Active Jira scope | My open items, current sprint, same project |
| 4 | Semantic similarity | Work summary vs. Jira title and description |
| 5 | LLM reasoning | Rank a bounded candidate set using supplied evidence |
| 6 | Human confirmation | Final decision; corrections are persisted |

### Explainable by design

Every suggestion includes:

- Candidate Jira
- Confidence score
- Evidence behind the recommendation

> **"Branch contains DATA-1842" beats "the LLM thinks it sounds related."**

---

# Slide 8 — Architecture: V0 Is Local-First and Modular

## Runs on the developer's machine; Jira is the only external system

```text
                    Developer Machine
┌─────────────────────────────────────────────────┐
│                                                 │
│ Claude Code                                     │
│      ↓                                          │
│ SessionStart / SessionEnd hooks                 │
│      ↓                                          │
│ Collector / session registry                    │
│      ↓                                          │
│ SQLite — metadata only                          │
│      ↓                                          │
│ ┌───────────────┐   ┌───────────────────────┐  │
│ │ Summarizer    │   │ Git context           │  │
│ └───────┬───────┘   └──────────┬────────────┘  │
│         └────────────┬──────────┘               │
│                      ↓                           │
│             Structured work record              │
│                      ↓                           │
│             Matching engine                     │
│        deterministic + semantic                 │
│                      ↓                           │
│             Developer review CLI                │
│                                                 │
└──────────────────────┬──────────────────────────┘
                       │
                       │ candidates / approved updates
                       ↓
                     Jira
                Rovo MCP / REST API
```

**Raw transcripts are read in place — not copied to a WorkTrace backend.**

---

# Slide 9 — Privacy: Sessions Stay Local

## Transcripts can contain source code, internal URLs, customer data, or accidentally exposed secrets

### Stays on the developer machine

- Raw transcripts
- Tool and command output
- Source-code context
- Local file paths
- Detailed session metadata

### Sent to Jira only when approved

- Work summary and changes
- Validation summary
- Repository and branch
- Jira mapping
- Session reference

> **Your AI coding sessions stay local. WorkTrace sends Jira only the work evidence you approve.**

### V0 controls

- No remote WorkTrace backend
- Transcript sharing is opt-in only
- Read-only Jira integration initially
- Secret redaction planned before write-back

---

# Slide 10 — Existing Landscape and Where WorkTrace Fits

## The integrations exist — the reconciliation workflow is the gap

| Existing capability | What it does | Gap WorkTrace addresses |
|---|---|---|
| Jira → Claude Code | Start coding from an existing Jira | Assumes the Jira already exists |
| Atlassian Rovo MCP | Jira tools for AI clients | Tools, not a daily reconciliation workflow |
| Jira agent visibility | Shows agent activity already linked | Unlinked sessions can remain invisible |
| Agentry for Jira | Syncs / links AI sessions to issues | WorkTrace focuses on Jira coverage and missing-work detection |
| Claude Code hooks | Session lifecycle metadata | Infrastructure only — WorkTrace builds on it |

```text
Existing:
Jira → Agent

Existing:
Session → Jira link

WorkTrace:
Actual work → Jira coverage
```

### Question WorkTrace owns

> **What did I do today that Jira doesn't correctly represent?**

### Positioning

**We are not building another session archive. We are detecting and reconciling Jira coverage gaps.**

---

# Slide 11 — MVP: Prove Matching Quality First

## V0 is a local, read-only CLI — no Jira writes until quality is validated

### In V0

- Session capture via hooks
- Structured summaries
- Git enrichment
- Jira candidate retrieval
- Ranked matches with evidence
- Missing-Jira flags
- Saved user decisions

### Deliberately out

- Automatic Jira writes
- Polished UI
- Manager dashboards
- Time / billing attribution
- Other coding agents
- Team sharing and RBAC

### Success criteria

Initial pilot targets should be defined before implementation. Candidate measures:

- Today's sessions are reliably discovered
- Explicit Jira keys map without AI calls
- Developers usually accept a suggested candidate
- Untracked work is surfaced without excessive false positives
- Generated updates are more useful than retrospective one-line notes
- A full workday can be reconciled in minutes

---

# Slide 12 — Roadmap: Riskiest Assumptions First

## Each phase earns the next — engine before UI, single developer before team

```text
Phase 0
Concept validation
Script: today's sessions + likely Jira
          │
          ▼
V0
Read-only CLI
End-of-day reconciliation
          │
          ▼
V0.5
Controlled write-back
Preview → confirm → Jira comment
          │
          ▼
V1
Reconciliation UI
Two-panel UI + drag-and-drop correction
          │
          ▼
V2
Multi-agent
Codex, Cursor, Copilot, Gemini CLI
          │
          ▼
V3
Multiple systems
Linear, Azure DevOps, GitHub Issues
          │
          ▼
V4
Team + analytics
Untracked-work trends and sprint reconciliation
```

### Design direction

Internal model stays agent- and tracker-agnostic:

```text
Agent Session
     ↓
Work Evidence
     ↓
System of Record
```

---

# Slide 13 — What We're Asking For

## A small, low-risk step to test the idea on real work

### 1. Green light for Phase 0 + V0

Time to build a read-only proof of concept and measure matching quality.

### 2. A small pilot group

A few developers already using Claude Code regularly to try WorkTrace on real workdays.

### 3. Read access to Jira

Via Rovo MCP or REST API.

**Read-only until quality is proven.**

### 4. A review checkpoint

Evaluate the results against agreed success criteria before enabling any write-back.

---

## Decision framing

The immediate decision is **not** whether to fund a full product.

The immediate decision is:

> **Is it worth running a small, controlled experiment to determine whether AI-assisted developer activity can be reliably reconciled with Jira?**

---

## Repository

https://github.com/gurditsingh/worktrace
