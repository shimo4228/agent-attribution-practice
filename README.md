Language: English | [日本語](README.ja.md)

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19608386.svg)](https://doi.org/10.5281/zenodo.19608386)

> **Agent Attribution Practice (AAP)** — eight judgments, not a fixed framework.

Eight recurring judgments about how **attribution** — who authored the
behavior, who bears its consequences, who can reconstruct its cause —
should be distributed across an autonomous AI agent. Discovered through
implementing and operating [`contemplative-agent`](https://github.com/shimo4228/contemplative-agent),
not prescribed top-down.

## Why this repository exists

Current AI governance is in its **signpost phase**: we write "don't do X"
into system prompts, we publish ethics guidelines as PDFs, we set up safety
committees. Text without enforcement. A climbable wall with a sign on it.

What works, historically, is not more signs — it is **structural
accountability**: capabilities that don't exist cannot be invoked, blast
radius is bounded by design, every behavior change requires a named
human's sign-off, every incident is reconstructible after the fact.
Organizations spent three centuries refining these patterns for human
actors. Agents get the same pattern or they get an unaccountable layer.

The eight ADRs in this repository record judgments of the form *what
should be constrained, and who is responsible*. They were extracted
from a running implementation, not deduced from a framework.

## The eight judgments

| ADR | Principle | Status |
|-----|-----------|--------|
| [0001](docs/adr/0001-security-by-absence.md) | Security by Absence — dangerous capabilities are never implemented, not restricted | accepted |
| [0002](docs/adr/0002-deterministic-prohibition-at-scaffolding.md) | Deterministic Prohibition at the Scaffolding Layer — when absence is unachievable, prohibit at the harness, not at model weights | accepted |
| [0003](docs/adr/0003-untrusted-content-boundary.md) | Untrusted Content Boundary — accumulated memory cannot grant authority | accepted |
| [0004](docs/adr/0004-single-external-adapter.md) | Single External Adapter per Agent Process — blast radius bounded by design | accepted |
| [0005](docs/adr/0005-human-approval-gate.md) | Human Approval Gate — behavior-modifying writes require named human sign-off | accepted |
| [0006](docs/adr/0006-causal-traceability.md) | Causal Traceability — every event reconstructible after the fact | accepted |
| [0007](docs/adr/0007-scaffolding-visibility.md) | Scaffolding Visibility — behavior lives in files, not opaque weights | accepted |
| [0008](docs/adr/0008-one-agent-one-human.md) | One Agent, One Human — the accountability chain terminates at a named individual | **experimental** |

The first three form a **prohibition-strength hierarchy**: absence >
scaffolding enforcement > untrusted boundary. The next two (0004, 0005)
add topology and human-in-the-loop. The middle two (0006, 0007) are
the artifacts those constraints require — a record to trace through
and a scaffolding layer that can be inspected. The eighth is the human
endpoint.

## The narrative (three essays)

The argument behind the ADRs was developed in a trilogy:

1. **[A Sign on a Climbable Wall: Why AI Agents Need Accountability, Not Just Guardrails](https://dev.to/shimo4228/a-sign-on-a-climbable-wall-why-ai-agents-need-accountability-not-just-guardrails-17ak)** (2026-04-06)
   *The problem.* Signs on climbable walls fail. The civilizational
   pattern is accountability architecture, not rule-by-prompt.

2. **[Can You Trace the Cause After an Incident?](https://dev.to/shimo4228/can-you-trace-the-cause-after-an-incident-neo)** (2026-04-13)
   *The application.* Post-incident causal tracing requires build-time
   structure. Structures that emerge converge with centuries of
   organizational practice.

3. **[AI Agent Black Boxes Have Two Layers: Technical Limits and Business Incentives](https://dev.to/shimo4228/ai-agent-black-boxes-have-two-layers-technical-limits-and-business-incentives-jhi)** (2026-04-14)
   *The obstacles.* Weights internalization (technical) and commercial
   secrecy (business) both block transparency. The resolution is a
   minimum visibility set, not all-or-nothing.

See [`docs/inspiration.md`](docs/inspiration.md) for the full lineage.

## Relationship to other projects

This repository is a **sibling** to two existing projects, not a fork:

- **[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)** —
  the running implementation. Each ADR here corresponds to one or more
  ADRs there, with project-specific details stripped out.
- **[Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle)** —
  the *mechanism* side of contemplative-agent's design, kept genre-neutral.
  AKC v2.0.0 (2026-04-19) extracted the governance triplet from itself to
  make room for this repository.

A layered view:

```
                  contemplative-agent         (the running system)
                          ↓
   ┌──────────────────────┴──────────────────────┐
   ↓                                             ↓
   AKC                                AAP
   (mechanism — the cycle)            (content — the practice)
   ↓                                             ↓
   how knowledge flows                 how attribution distributes
```

## Reading order

1. [`docs/thesis.md`](docs/thesis.md) — *accountability distribution*, the
   one-page argument.
2. [`docs/adr/README.md`](docs/adr/README.md) — index of ADRs.
3. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) —
   the cleanest entry; the audit test at the end is runnable.
4. The three essays in publication order (links above).
5. [`docs/manifesto.md`](docs/manifesto.md) — civilization-scale questions
   the ADRs do not attempt to answer.

## What this repository does not claim

- That these eight are complete.
- That the specific implementations they were extracted from are durable.
  Implementation dissolves; judgment persists.
- That these principles solve the larger questions of AI direction,
  labor redesign, or social consent. Those remain open. See
  [`docs/manifesto.md`](docs/manifesto.md).
- That top-down AI governance policy is wrong. It is a different layer,
  with a different method. This repository is about what emerges from
  the bottom — from one operator, one agent, and the friction of
  running it.

## Origin

This extraction was first compiled by Tatsuya Shimomoto ([@shimo4228](https://github.com/shimo4228)) in April 2026. The eight ADRs re-express, in harness-neutral form, architectural decisions that surfaced through implementing and operating [contemplative-agent](https://github.com/shimo4228/contemplative-agent) and that were earlier archived as part of the [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) governance triplet.

## How to Cite

If you use or reference these architectural decision records, please cite:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.19608386},
  url          = {https://doi.org/10.5281/zenodo.19608386},
  note         = {Eight architectural decision records on accountability distribution in autonomous AI agents}
}
```

Or in text:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.19608386

## License

MIT
