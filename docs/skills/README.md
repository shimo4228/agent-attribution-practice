# Design-pattern skills (moved to standalone repositories)

This directory previously hosted AAP's design-pattern skill. The skills are
now published as standalone, installable skill repositories; the paired ADRs
(the "why") remain in this repository.

| Skill | Repository | Paired ADR(s) | One-line summary |
|-------|------------|---------------|------------------|
| llm-agent-security-principles | [shimo4228/llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles) | [ADR-0001](../adr/0001-security-by-absence.md), [ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md), [ADR-0003](../adr/0003-untrusted-content-boundary.md), [ADR-0004](../adr/0004-single-external-adapter.md) | Structural security for autonomous LLM agents: Security by Absence, Untrusted Content Boundary, One External Adapter |
| agent-adoption-triage | [shimo4228/agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage) | [ADR-0009](../adr/0009-triage-before-autonomy.md), [ADR-0010](../adr/0010-phase-separation.md) | Five-question triage routing AI work to the right quadrant before an agent is built; installable form of the [`quadrants/`](../quadrants/README.md) navigator |
| agent-observability-patterns (3 skills) | [shimo4228/agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns) | [ADR-0006](../adr/0006-causal-traceability.md), [ADR-0005](../adr/0005-human-approval-gate.md) | Observation precedes intervention: replayable audit logs (the reconstruction substrate ADR-0006 demands), read-only instruments, and shadow-mode validation (the evidence a named approver consumes under ADR-0005) |

## Installing

```bash
# manual copy (run inside the respective skill repository)
cp -r skills/llm-agent-security-principles ~/.claude/skills/llm-agent-security-principles
cp -r skills/agent-adoption-triage ~/.claude/skills/agent-adoption-triage
cp -r skills/replayable-audit-logs ~/.claude/skills/replayable-audit-logs
cp -r skills/read-only-instruments ~/.claude/skills/read-only-instruments
cp -r skills/shadow-mode-validation ~/.claude/skills/shadow-mode-validation

# or via SkillsMP
/skills add shimo4228/llm-agent-security-principles
/skills add shimo4228/agent-adoption-triage
/skills add shimo4228/agent-observability-patterns
```

## Why it moved

Hosting an installable skill two levels deep under `docs/` made it hard
to discover and impossible to install with standard tooling. The skills
now follow the standard Agent Skills repository layout while the ADR
pairing survives as cross-links in both directions.
