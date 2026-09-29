# Privacy and Data Handling

## Principle

WorkTrace should be **local-first by default**.

Claude Code sessions can contain sensitive information that should not automatically be copied into Jira or another external service.

---

# What Can Exist in a Claude Transcript

A transcript may contain:

- proprietary source code
- internal architecture
- database names
- internal URLs
- stack traces
- command output
- customer information
- environment details
- credentials accidentally printed in logs
- secrets-adjacent information
- prompts and reasoning specific to internal systems

Therefore raw transcript synchronization should not be the default behavior.

---

# Recommended Default

Keep locally:

```text
raw transcript
tool outputs
source-code context
local file paths
detailed session metadata
```

Send externally only when required:

```text
work summary
implementation changes
validation summary
repository
branch
Jira mapping
session reference/id
```

---

# Jira Write-Back

A typical Jira update should contain derived work evidence rather than full conversation history.

Example:

```text
Implementation update

Implemented XML source support.

Changes:
• Added XML reader
• Updated YAML validation
• Added unit tests

Validation:
• 23 tests passed

Repository:
etl-framework

Branch:
DATA-1842-add-xml-reader
```

This provides useful traceability without exposing unnecessary transcript detail.

---

# Opt-In Transcript Sharing

If WorkTrace later supports transcript attachment or synchronization, it should be:

- disabled by default
- explicit
- clearly scoped
- reviewable before upload

Users should know exactly what content will leave the machine.

---

# Secret and Sensitive Data Filtering

Before external write-back, WorkTrace should eventually support redaction for patterns such as:

- API keys
- tokens
- passwords
- private keys
- connection strings
- authorization headers
- secrets from .env output

The MVP does not need a perfect secret scanner, but the architecture should not assume all transcript content is safe to publish.

---

# Data Retention

For V0:

- store only what is needed
- reference transcript paths instead of copying transcript content
- allow local records to be deleted
- avoid building a remote WorkTrace backend

A centralized service can be considered later only if team features require it.

---

# Privacy Positioning

A strong product message is:

> **Your AI coding sessions stay local. WorkTrace sends Jira only the work evidence you approve.**

This can become an important differentiator for enterprise adoption.
