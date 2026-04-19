# Inspiration and Lineage

## The three articles

The narrative spine of this repository is a trilogy of essays published
in April 2026:

1. **[登れる壁に看板を立てても意味がない — AIエージェントに必要なのはガードレールではなくアカウンタビリティだ](https://zenn.dev/shimo4228/articles/ai-agent-accountability-wall)** (2026-04-06)
   > *Problem statement.* Signs on climbable walls are meaningless.
   > From Plato's Ring of Gyges (root privilege without audit log) through
   > Hobbes' reputation-based access control, to the three engineering
   > enforcement patterns (physical constraint / consequence / internalized
   > value) — the "signpost" pattern has been known to fail for 2400 years.
   > Current AI governance is in the signpost phase. What is needed is
   > accountability architecture.

2. **[事故のあとで因果を辿れるか — エージェント設計が組織論に収斂するまで](https://zenn.dev/shimo4228/articles/agent-causal-traceability-org-adoption)** (2026-04-13)
   > *Application.* Post-incident causal tracing requires build-time
   > structure. The cost asymmetry (incident cost >> steady-state cost)
   > pushes rational investment upstream. The structures that emerge —
   > segregation of duties, least privilege, four-eyes approval, audit
   > trails — are structurally identical to the practices organizations
   > have refined over centuries.

3. **[AIエージェントのブラックボックスは二層ある — 技術の限界とビジネスの都合](https://zenn.dev/shimo4228/articles/agent-blackbox-capitalism-timescale)** (2026-04-14)
   > *Obstacle analysis.* Blackbox has two layers: model-weights
   > internalization (technical) and commercial secrecy (business). Both
   > block the transparency causal tracing requires. The resolution is
   > not "publish everything" or "hide everything," but defining the
   > minimum set that makes causal tracing possible.

Together, these three form the argument: current AI governance is
signpost-era; structural accountability is what works; the obstacles
are internalization into weights and commercial opacity; the way
forward is defining the minimum structural set that makes
accountability chains possible.

## Related repositories

### contemplative-agent (implementation)

[contemplative-agent](https://github.com/shimo4228/contemplative-agent)
is the running implementation that the eight ADRs were extracted from.
Each principle in this repository corresponds to one or more ADRs in
`contemplative-agent/docs/adr/`, typically with project-specific
details that were removed during extraction here:

| Principle in this repo | Corresponding contemplative-agent ADR |
|------------------------|---------------------------------------|
| ADR-0001 Security by Absence | [ADR-0007 Security Boundary Model](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0007-security-boundary-model.md) (partially) |
| ADR-0002 Deterministic Prohibition at the Scaffolding Layer | PreToolUse hooks (`block-episode-logs-{read,bash,grep}.sh`) + `summarize_record` quarantine in [contemplative-agent](https://github.com/shimo4228/contemplative-agent); rationale documented in `docs/security/2026-04-01-episode-log-access-control.md` and the ADR-0021 implementation audit (ADR-0028 / ADR-0029) |
| ADR-0003 Untrusted Content Boundary | [ADR-0007 Security Boundary Model](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0007-security-boundary-model.md) (partially) |
| ADR-0004 Single External Adapter | [ADR-0015 One External Adapter Per Agent](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0015-one-external-adapter-per-agent.md) |
| ADR-0005 Human Approval Gate | [ADR-0012 Human Approval Gate](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0012-human-approval-gate.md) |
| ADR-0006 Causal Traceability | Implicit across the codebase; first named in the 2026-04-13 essay |
| ADR-0007 Scaffolding Visibility | Implicit in the fully-materialized scaffolding design; first named in the 2026-04-14 essay |
| ADR-0008 One Agent, One Human | First formalized in this repository (experimental) |

### agent-knowledge-cycle (AKC)

[AKC](https://github.com/shimo4228/agent-knowledge-cycle) (DOI
`10.5281/zenodo.19200727`) is a sibling project that captures the
*mechanism* side of what contemplative-agent produces: the six-phase
knowledge cycle (Research → Extract → Curate → Promote → Measure →
Maintain) that keeps an agent's skills, rules, and docs aligned with
reality over time.

In v1.x, AKC included three ADRs that turned out to be genre-specific
(Security by Absence / Single External Adapter / Untrusted Content
Boundary) rather than mechanism-neutral. AKC v2.0.0 (2026-04-19) extracted
them into `_archive/akc-security-triplet-2026-04/`, with the archive
README noting they would "be re-expressed... in a sibling genre library
at a later date." *This repository is that sibling.*

The relationship:

- **AKC**: the cycle (mechanism). Genre-neutral. How knowledge flows.
- **This repository**: the constraints (content, for autonomous AI
  agents). How accountability is distributed.

Both are extractions from contemplative-agent, along different axes.

## Why the extraction path looked like this

The shortest version:

1. contemplative-agent was built to solve a specific problem (run a
   contemplative AI agent on a social platform).
2. Architectural ideas surfaced that turned out to matter structurally
   regardless of the problem.
3. Three were extracted into AKC v1.x because they co-occurred with the
   knowledge-cycle mechanism.
4. AKC v2.0.0 concluded that cycle-and-constraints are actually
   separable, and moved to mechanism-only.
5. This repository re-expresses the constraints plus the ones that had
   surfaced in the meantime (the three-article trilogy, the
   1-agent-1-human experiment, and the deterministic-prohibition
   hierarchy that emerged from the MINJA / episode-log access-control
   work).

The re-expression required rewriting each ADR to be harness-neutral.
The original decisions remain dated to when they were first made;
the re-expression dates the placement in this repository.
