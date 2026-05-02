Language: English | [日本語](README.ja.md)

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19920228.svg)](https://doi.org/10.5281/zenodo.19920228)

> **Agent Attribution Practice (AAP)** — ten judgments paired with four Business AI Quadrants, not a fixed framework.

Ten recurring judgments about how **attribution** — who authored the
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

The ten ADRs in this repository record judgments of the form *what
should be constrained, and who is responsible*. They were extracted
from a running implementation, not deduced from a framework.

## The ten judgments

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
| [0010](docs/adr/0010-phase-separation.md) | Phase Separation Between Design and Operation — operation-phase placement of the Autonomous Agentic Loop Quadrant requires a recorded Phase-crossing decision | **experimental** |

The first three form a **prohibition-strength hierarchy**: absence >
scaffolding enforcement > untrusted boundary. The next two (0004, 0005)
add topology and human-in-the-loop. The middle two (0006, 0007) are
the artifacts those constraints require — a record to trace through
and a scaffolding layer that can be inspected. The eighth is the human
endpoint. ADRs 0009 and 0010 form a **triage pair**: 0009 asks whether
the work belongs in an architecture where accountability distribution
operates at all (the problem-space triage), and 0010 surfaces the
Phase-crossing decision when the autonomous loop is placed in the
operation phase (the time-axis triage). Phase and Quadrant are
independent dimensions; ADR-0010 is procedural, not architectural.

## The four Business AI Quadrants

Paired with the ten ADRs as the diagnostic frame for adoption:

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

The ten ADRs and the four quadrants form a **two-axis structure**:
the ADRs answer per-question (*what should be constrained, who is
responsible*); the quadrants route the work to where those answers
apply. Phase (design vs operation) is independent of Quadrant — every
Quadrant can appear in either phase, and ADR-0010's load-bearing rule
is about one specific placement (operation-phase Quadrant 4), not a
phase-to-quadrant mapping.

## The narrative (essays)

The argument behind the ADRs was developed across a seven-essay
sequence published in April–May 2026. The first three form the
original trilogy (problem statement → post-incident causal tracing →
two-layer blackbox analysis); the next four are the architectural
follow-up (four-quadrant triage → vocabulary diagnosis with the
principled attribution gap → design / operation phase distinction →
skill-design gradient resolution).

See [`docs/inspiration.md`](docs/inspiration.md) for the full lineage
with per-essay summaries and URLs.

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

## Relationship to industry mechanism layer

Through 2026 Q2, several industry releases populated the *mechanism
layer* that AAP's principles describe — sub-millisecond policy
gates, agent-identity primitives, sponsor systems, cross-vendor
audit, cross-cloud registry sync.

What the mechanism layer does not ship is the judgment layer. A
sponsor can be assigned, but who *should* be the sponsor and what
commitment that acknowledges is not in the product. A cross-cloud
registry can be synced, but the Single External Adapter design
judgment (bounding blast radius at design time, not observing it
post-hoc) is not in the product. A policy engine can intercept agent
actions, but the prohibition-strength hierarchy (absence >
scaffolding > untrusted boundary) that decides where each
prohibition belongs is not in the product. AAP fills this judgment
layer; the two layers are complementary, and the mechanism layer's
adoption increases — not diminishes — the need for the judgment
layer.

The per-artifact mapping (which vendor mechanism instantiates which
ADR, what each artifact ships and what it does not) is maintained
separately in [`docs/industry-mapping.md`](docs/industry-mapping.md),
a deliberately time-bound document that decays as products evolve;
the ADRs themselves stay clean.

## Reading order

1. [`docs/thesis.md`](docs/thesis.md) — *accountability distribution*, the
   one-page argument.
2. [`docs/adr/README.md`](docs/adr/README.md) — index of ADRs.
3. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) —
   the cleanest entry; the audit test at the end is runnable.
4. The six essays in publication order (links above).
5. [`docs/quadrants/`](docs/quadrants/) — adoption navigator: decision
   tree, governance mapping, case studies, anti-patterns.
6. [`docs/manifesto.md`](docs/manifesto.md) — civilization-scale questions
   the ADRs do not attempt to answer.

**Japanese readers:** see [`README.ja.md`](README.ja.md),
[`docs/thesis.ja.md`](docs/thesis.ja.md),
[`docs/glossary.ja.md`](docs/glossary.ja.md), and the Japanese
mirrors of the quadrants navigator
([`docs/quadrants/README.ja.md`](docs/quadrants/README.ja.md) and
its siblings). ADRs remain English-only per repository convention.

## What this repository does not claim

- That these ten are complete.
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

This extraction was first compiled by Tatsuya Shimomoto ([@shimo4228](https://github.com/shimo4228)) in April 2026. The ten ADRs and the four Business AI Quadrants re-express, in harness-neutral form, architectural decisions and triage judgments that surfaced through implementing and operating [contemplative-agent](https://github.com/shimo4228/contemplative-agent) and through the seven-essay narrative spine published in April–May 2026; the first eight ADRs were earlier archived as part of the [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) governance triplet, while ADR-0009 and the Quadrants navigator emerged from the 2026-04-29 / 04-30 architectural follow-up essays, and ADR-0010 from the 2026-05-01 phase-distinction essay together with the 2026-05-02 skill-design gradient essay.

## How to Cite

If you use or reference these architectural decision records, please cite:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.19920228},
  url          = {https://doi.org/10.5281/zenodo.19920228},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (two experimental), paired with four Business AI Quadrants as the diagnostic frame and a Phase / Quadrant two-axis structure}
}
```

Or in text:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.19920228

## License

MIT
