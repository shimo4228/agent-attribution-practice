# Design-pattern skills (moved to a standalone repository)

This directory previously hosted AAP's design-pattern skill. It is now
published as a standalone, installable skill repository; the paired ADRs
(the "why") remain in this repository.

| Skill | Repository | Paired ADR(s) | One-line summary |
|-------|------------|---------------|------------------|
| llm-agent-security-principles | [shimo4228/llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles) | [ADR-0001](../adr/0001-security-by-absence.md), [ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md), [ADR-0003](../adr/0003-untrusted-content-boundary.md), [ADR-0004](../adr/0004-single-external-adapter.md) | Structural security for autonomous LLM agents: Security by Absence, Untrusted Content Boundary, One External Adapter |

## Installing

```bash
# manual copy
cp -r skills/llm-agent-security-principles ~/.claude/skills/llm-agent-security-principles

# or via SkillsMP
/skills add shimo4228/llm-agent-security-principles
```

## Why it moved

Hosting an installable skill two levels deep under `docs/` made it hard
to discover and impossible to install with standard tooling. The skill
now follows the standard Agent Skills repository layout while the ADR
pairing survives as cross-links in both directions.
