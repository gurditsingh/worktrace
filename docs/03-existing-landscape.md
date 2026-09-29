# Existing Landscape

## Why This Matters

WorkTrace is being designed in an active integration space.

There are already products and platform capabilities that connect Jira and AI coding agents.

The differentiation therefore should not be:

> "Claude can integrate with Jira."

Instead, WorkTrace focuses on **daily work reconciliation and missing-work detection**.

---

## Atlassian — Jira to AI Coding Tools

Atlassian supports workflows where an existing Jira work item can be opened in supported AI coding tools such as Claude Code.

The flow is primarily:

```text
Jira
  ↓
Existing work item
  ↓
Open in coding tool
  ↓
Claude Code
```

This works well when the Jira already exists.

### Gap addressed by WorkTrace

WorkTrace focuses on the reverse problem:

```text
Claude work already happened
        ↓
Which Jira does it belong to?
        ↓
Does a Jira even exist?
```

---

## Atlassian Rovo MCP

Atlassian provides an official Model Context Protocol integration for Atlassian products.

It can provide AI clients with Jira capabilities such as:

- search work
- retrieve work items
- create work
- edit work
- transition work
- add comments

Rovo MCP is a strong integration mechanism for WorkTrace.

### Gap addressed by WorkTrace

Rovo MCP provides tools.

WorkTrace provides the workflow and reconciliation intelligence around those tools.

---

## Jira Agent Sessions / Agent Visibility

Jira has been adding visibility into AI-agent activity and agent sessions.

These capabilities help when sessions are already associated with Jira work.

### Gap addressed by WorkTrace

WorkTrace starts with the sessions that may never have been linked:

> What meaningful agent-assisted engineering work happened today that Jira does not currently represent?

---

## Agentry for Jira

A close adjacent product is **Agentry for Jira**, which advertises Claude Code and Codex integration.

Its public product description includes capabilities such as:

- synchronizing AI conversations into Jira
- linking a session to the issue it worked on
- storing AI session information
- backing up and restoring session configuration

This is the closest identified product to the session-to-Jira part of WorkTrace.

### What WorkTrace should differentiate on

Do not position WorkTrace as:

> "Link a Claude session to Jira."

Instead position it as:

> **Detect, understand, and reconcile today's AI-assisted engineering work against Jira coverage.**

The key flow is:

```text
Today's sessions
      ↓
Which represent meaningful work?
      ↓
Which Jira already covers them?
      ↓
Which Jira is missing?
      ↓
What should be updated or created?
```

---

## Claude Code Hooks

Claude Code lifecycle hooks expose session metadata that can be used to build a local collector.

Relevant events include:

```text
SessionStart
SessionEnd
```

Useful metadata can include:

```text
session_id
transcript_path
cwd
source
```

This means WorkTrace does not need to scrape the Claude terminal UI to discover activity.

---

## Landscape Summary

| Existing capability | What it does | WorkTrace gap |
|---|---|---|
| Jira → Claude Code | Starts coding work from existing Jira | Assumes Jira already exists |
| Atlassian Rovo MCP | Provides Jira tools to AI clients | Does not provide daily reconciliation |
| Jira agent visibility | Shows agent activity linked to Jira | Unlinked sessions can remain invisible |
| Agentry for Jira | Syncs and links AI sessions | WorkTrace focuses on detecting and reconciling missing work |
| Claude hooks | Exposes session lifecycle | Infrastructure only; no Jira reasoning |

---

## WorkTrace Positioning

The useful mental model is:

```text
Existing:
Jira → Agent

Existing:
Agent Session → Jira storage/link

WorkTrace:
Actual work → Jira coverage reconciliation
```

The question WorkTrace owns is:

> **What work did I actually do today that Jira does not correctly represent?**
