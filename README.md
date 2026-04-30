Language: English | [日本語](README.ja.md)

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652014.svg)](https://doi.org/10.5281/zenodo.19652014)

> **Agent Attribution Practice (AAP)** — nine judgments paired with four Business AI Quadrants, not a fixed framework.

Nine recurring judgments about how **attribution** — who authored the
behavior, who bears its consequences, who can reconstruct its cause —
should be distributed across an autonomous AI agent, paired with the
four-quadrant diagnostic frame that routes a piece of work to the
architecture preserving that distribution. Discovered through
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

The nine ADRs in this repository record judgments of the form *what
should be constrained, and who is responsible*. They were extracted
from a running implementation, not deduced from a framework.

## The nine judgments

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
| [0009](docs/adr/0009-triage-before-autonomy.md) | Triage Before Autonomy — adopting an autonomous-loop architecture commits the system to a non-removable attribution gap | **experimental** |

The first three form a **prohibition-strength hierarchy**: absence >
scaffolding enforcement > untrusted boundary. The next two (0004, 0005)
add topology and human-in-the-loop. The middle two (0006, 0007) are
the artifacts those constraints require — a record to trace through
and a scaffolding layer that can be inspected. The eighth is the human
endpoint. The ninth (0009) is the architectural triage that
determines, before any of the other eight become load-bearing, whether
the work belongs in an architecture where accountability distribution
operates at all.

## The four Business AI Quadrants

Paired with the nine ADRs as the diagnostic frame for adoption:

|  | Pre-defined workflow | Exploratory |
|---|---|---|
| **Deterministic** | (1) Script Quadrant | (2) Algorithmic Search Quadrant |
| **Semantic-judgment** | (3) LLM Workflow Quadrant | (4) Autonomous Agentic Loop Quadrant |

Most current LLM applications belong to the **LLM Workflow Quadrant**
(deterministic control flow + bounded LLM calls with named roles),
not the **Autonomous Agentic Loop Quadrant**. Routing the former into
the latter is the structural source of much of the accountability
collapse the trilogy diagnosed; running the latter without a
pre-named gap-bearer is the failure mode ADR-0009 is designed to
prevent. See [`docs/quadrants/`](docs/quadrants/) for the navigator
(decision tree, governance mapping, case studies, anti-patterns).

The nine ADRs and the four quadrants form a **two-axis structure**:
the ADRs answer per-question (*what should be constrained, who is
responsible*); the quadrants route the work to where those answers
apply.

## The narrative (essays)

The argument behind the ADRs was developed across the following essays:

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

4. **[Where ReAct Agents Are Actually Needed in Business](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do)** (2026-04-29)
   *The architectural follow-up.* Business AI sorted into four quadrants
   reveals that ReAct agents are legitimately needed only in (4) exploratory
   tasks. The current ecosystem's category error of forcing (4) architecture
   onto (3) workflows is at the root of the accountability picture's collapse.

5. **[The LLM Workflow Quadrant Is Missing from Our Vocabulary](https://dev.to/shimo4228/3-the-llm-workflow-quadrant-is-missing-from-our-vocabulary-n18)** (2026-04-30)
   *The vocabulary diagnosis and the missing accountability subject.*
   The industry has no positive name for the LLM Workflow Quadrant, so
   non-deterministic work gets routed through a ReAct loop by elimination.
   Discussion concentrates on this artificial redirect impossibility,
   while the principled attribution gap intrinsic to ReAct (runtime blend
   dissolves the post-hoc separability of contributions) and the absent
   accountability subject — autonomy demands responsibility but agents
   are not currently legal subjects who can bear it — stay in the shadows.

6. **[Is ReAct Needed in Production? — Separating Design and Operation Phases](https://dev.to/shimo4228/is-react-needed-in-production-separating-design-and-operation-phases-4ll0)** (2026-05-01)
   *The temporal axis.* Business work splits into a design phase and an
   operation phase. Design demands flexibility, operation demands
   predictability — their optimization axes are inverted. ReAct belongs
   to the design phase. The category error of routing (3) work to (4)
   architecture, diagnosed in the previous two essays, descends one
   layer: it is rooted in the conflation of these two phases. ReAct's
   legitimate domain narrows to design-phase exploratory tasks
   (coding, Deep Research, unknown-environment exploration); production
   systems run on (1) Script + (3) LLM Workflow combinations once design
   is complete.

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

A layered view — the three co-evolve through mutual feedback:

```
  ─── theory layer ────────────────────────────────────────────

        AKC  ◄─────────────────────────────────►  AAP
        (mechanism — the cycle)                   (content — the practice)
        how knowledge flows                       how attribution distributes

              ▲                                         ▲
              │                                         │
              ▼                                         ▼

  ─── implementation layer ────────────────────────────────────

                         contemplative-agent
                         (the running system)
```

Running the implementation surfaces friction; friction yields
mechanism patterns (AKC) and attribution judgments (AAP); refined
theory loops back to reshape the implementation.

## Reading order

1. [`docs/thesis.md`](docs/thesis.md) — *accountability distribution*, the
   one-page argument.
2. [`docs/adr/README.md`](docs/adr/README.md) — index of ADRs.
3. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) —
   the cleanest entry; the audit test at the end is runnable.
4. The five essays in publication order (links above).
5. [`docs/quadrants/`](docs/quadrants/) — adoption navigator: decision
   tree, governance mapping, case studies, anti-patterns.
6. [`docs/manifesto.md`](docs/manifesto.md) — civilization-scale questions
   the ADRs do not attempt to answer.

## What this repository does not claim

- That these nine are complete.
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

This extraction was first compiled by Tatsuya Shimomoto ([@shimo4228](https://github.com/shimo4228)) in April 2026. The nine ADRs and the four Business AI Quadrants re-express, in harness-neutral form, architectural decisions and triage judgments that surfaced through implementing and operating [contemplative-agent](https://github.com/shimo4228/contemplative-agent) and through the five-essay narrative spine published in April 2026; the first eight ADRs were earlier archived as part of the [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) governance triplet, while ADR-0009 and the Quadrants navigator emerged from the 2026-04-29 / 04-30 architectural follow-up essays.

## How to Cite

If you use or reference these architectural decision records, please cite:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.19652014},
  url          = {https://doi.org/10.5281/zenodo.19652014},
  note         = {Nine architectural decision records on accountability distribution in autonomous AI agents (one experimental), with four Business AI Quadrants as diagnostic frame}
}
```

Or in text:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.19652014

## License

MIT
