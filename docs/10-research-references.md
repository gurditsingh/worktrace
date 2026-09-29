# Research and References

This document records the external products and platform capabilities considered while shaping WorkTrace.

> Note: platform behavior changes over time. Re-check current documentation before implementation decisions.

---

## Claude Code Hooks

Claude Code hook documentation:

https://code.claude.com/docs/en/hooks

Relevant to:

- SessionStart
- SessionEnd
- session_id
- transcript_path
- cwd
- session source

WorkTrace can use lifecycle hooks to discover which sessions were actually used without scraping the terminal interface.

---

## Atlassian — Open a Jira Work Item in an AI Coding Tool

https://support.atlassian.com/jira-software-cloud/docs/open-a-jira-work-item-in-an-ai-coding-tool/

Relevant to Jira-first coding-agent workflows.

The key distinction for WorkTrace is that its main flow begins after work may already have happened.

---

## Atlassian Rovo MCP

https://developer.atlassian.com/cloud/rovo-mcp/

Relevant to:

- retrieving Jira context
- searching work items
- creating/updating Jira through an AI client

Potential integration layer for the MVP.

---

## Atlassian — Agents Dashboard

https://support.atlassian.com/jira-software-cloud/docs/use-the-agents-dashboard/

Relevant to Atlassian's current agent-session visibility and reporting capabilities.

---

## Agentry for Jira

Atlassian Marketplace:

https://marketplace.atlassian.com/apps/2151076408/%26

Agentry is the closest identified adjacent product because it advertises Claude Code/Codex session synchronization and Jira linking.

WorkTrace should differentiate around:

- end-of-day reconciliation
- untracked-work detection
- automatic candidate matching
- Jira coverage analysis
- evidence-based Jira summaries

rather than basic session-to-Jira linking.

---

## Agentry Privacy and Security

https://marketplace.atlassian.com/apps/2151076408/agentry-for-jira-claude-code-codex-integration?tab=privacy-and-security

Useful for understanding how an existing integration approaches synchronization of local AI session content.

---

# Research Conclusion

The market already contains strong building blocks for:

```text
Jira → Agent
```

and:

```text
Agent Session → Jira
```

WorkTrace is intentionally centered on:

```text
Actual AI-assisted work
        ↓
Does Jira represent it?
        ↓
If yes, map/update it
If no, surface it
```

This is the product gap the project should continue validating.
