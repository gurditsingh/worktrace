# Problem Statement

## Summary

Developers increasingly use AI coding agents such as Claude Code for implementation, debugging, investigation, testing, and research.

Jira, however, still depends heavily on developers creating the right work item at the right time and keeping it updated manually.

That creates a growing gap between:

```text
Work that actually happened
        vs.
Work Jira says happened
```

WorkTrace exists to close that gap.

---

## The Core Problem

A common development workflow now looks like this:

```text
Developer receives a task
        ↓
Opens Claude Code
        ↓
Investigates / implements / tests
        ↓
Work is completed
        ↓
Developer realizes no Jira was created
        ↓
Creates a high-level Jira afterward
        ↓
Detailed work context is lost
```

The Jira item may technically exist, but it often does not accurately reflect the engineering work that took place.

---

## Problem 1 — Work Starts Before Jira Exists

A developer may begin working immediately in Claude Code without first creating or locating a Jira item.

Example:

```text
09:00  Open Claude Code
09:10  Start debugging a data pipeline issue
09:30  Identify root cause
10:00  Implement fix
10:20  Run tests
10:30  Work complete

Jira created: No
```

The actual work is represented in the Claude session and Git activity, but not in the system of record.

---

## Problem 2 — Jira Is Created Retrospectively

Later, the developer remembers that the work needs to be represented in Jira.

A retrospective Jira might look like:

```text
Title:
Fix pipeline issue

Description:
Fixed pipeline issue.

Status:
Done
```

This captures almost none of the useful implementation context.

The original Claude session may contain:

- the problem being investigated
- root-cause analysis
- alternatives considered
- code changes
- files modified
- tests executed
- validation results
- implementation decisions

Most of that is lost when Jira is reconstructed manually after the fact.

---

## Problem 3 — Work Evidence Is Fragmented

Relevant implementation evidence may exist across:

- Claude Code transcripts
- Git branches
- commit messages
- pull requests
- local files
- terminal activity
- test results
- Jira

There is no single reconciliation step that answers:

> What did I actually work on today, and is all of it correctly represented in Jira?

---

## Problem 4 — Jira Becomes an Incomplete System of Record

If work is missing or poorly described in Jira:

- engineering effort is under-represented
- investigation work disappears
- stories become too broad
- implementation details are lost
- sprint reporting becomes less accurate
- traceability between request and implementation is reduced
- teams cannot easily understand how AI-assisted work contributed to delivery

---

## Example Day

| Time | Actual work | Jira state |
|---|---|---|
| 9:00–10:30 | Add XML reader to ETL framework | No Jira created |
| 11:00–12:00 | Debug Glue 5 compatibility | Possibly related to an existing migration story |
| 2:00–3:30 | GDPR purge fix | Jira exists but has no implementation update |
| 5:00 PM | Developer remembers tracking | Creates one broad ticket afterward |

The developer did meaningful work throughout the day, but Jira does not accurately represent it.

---

## Problem Statement

> **How can we automatically identify the AI-assisted work a developer actually performed, map that work to existing Jira items, detect work with no Jira coverage, and produce accurate Jira-ready updates with minimal manual effort?**

---

## Why This Problem Becomes More Important With AI Coding Agents

Before coding agents, much of a developer's work trail was visible through:

```text
ticket → branch → commits → pull request
```

With AI coding agents, a meaningful part of the engineering process can now happen inside long-lived conversational sessions:

```text
problem exploration
      ↓
agent conversation
      ↓
tool calls
      ↓
code changes
      ↓
validation
      ↓
follow-up changes
```

Those sessions are increasingly valuable evidence of the work performed.

WorkTrace treats those sessions as a new source of engineering activity that can be reconciled with Jira rather than ignored.
