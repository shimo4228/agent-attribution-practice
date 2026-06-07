Language: English | [日本語](README.ja.md)

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652013.svg)](https://doi.org/10.5281/zenodo.19652013)

> **Agent Attribution Practice (AAP)** — ten judgments paired with four Business AI Quadrants, not a fixed framework.

📄 **Companion papers**:
- *Distributing Accountability, Not Capability: Phase Separation and the LLM Workflow Quadrant in Autonomous AI Agent Architectures* — [doi:10.5281/zenodo.20353789](https://doi.org/10.5281/zenodo.20353789) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6817598)
- *The Two-Layer Black Box: Operator Visibility, Commercial Secrecy, and a Minimum Disclosure Set for Accountable Autonomous AI Agents* — [doi:10.5281/zenodo.20355907](https://doi.org/10.5281/zenodo.20355907) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6823878)

<details>
<summary>AI-facing reading order</summary>

1. [`graph.jsonld`](graph.jsonld) — canonical machine-readable relationship map (Quadrants, ADRs, prohibition levels, Phase)
2. [`llms.txt`](llms.txt) — compact navigation index
3. [`llms-full.txt`](llms-full.txt) — consolidated factual reference
4. README and project-specific docs — narrative and detail

For the canonical relationship map of shimo4228's research ecosystem, see:
https://github.com/shimo4228/shimo4228/blob/main/graph.jsonld

</details>

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

Beyond the spine, a companion essay opens a **social-consequence
layer** — a normative extension that sits on top of the seven essays,
not within them. It reads the same internal judgments from the outside:
externalized accountability does not disappear, and whether it flows
into institutions or converges into violence depends on whether the
consequence can be named. On that reading, accountability distribution
is not only governance but a violence-prevention mechanism. The
harness-neutral structural claim is kept separate from the ADRs in
[`docs/social-consequence.md`](docs/social-consequence.md); the concrete
grounding lives in the essay itself.

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

> The next two sections position AAP against external mechanism layers
> (vendor products, then governance frameworks). Skip to
> [Reading order](#reading-order) if you only want the entry points.

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

## Relationship to AI governance frameworks

AAP's ADRs and Quadrants are also read against national /
international AI governance frameworks. The mapping covers the
**NIST AI Risk Management Framework 1.0** (NIST.AI.100-1, January
2023) with its **Generative AI Profile** (NIST.AI.600-1, July
2024), **ISO/IEC 42001:2023** AI Management System (first edition,
December 2023), and the **EU AI Act** (Regulation (EU) 2024/1689,
in force August 2024, phased application through 2027). OECD AI
Principles are deferred to a later phase — their decay cadence and
clause granularity differ enough that bundling them in would
create churn.

What the frameworks ship is the *structure* — NIST organizes AI
risk management around four functions (GOVERN, MAP, MEASURE,
MANAGE) and the GAI Profile adds generative-AI risk categories;
ISO 42001 specifies an AI Management System built on the Annex
SL Harmonized Structure (shared with ISO 27001, ISO 9001, ISO
14001) and a PDCA cycle, with Annex A controls covering policies,
roles, lifecycle, data, transparency, and third-party
relationships; the EU AI Act ships a *risk-tiered obligation
regime* (prohibited practices, the high-risk provider/deployer
duty stack — human oversight, record-keeping, robustness — and
transparency duties). What they do not ship is the *judgment
layer* that populates these structures for the autonomous-agent
subset:
when a NIST function should be answered by absence rather than
by restriction, which AIMS clause requires a non-LLM gate, where
the gap-bearer named under ADR-0009 sits in the ISO 42001
accountability chain, what the Phase-crossing decision adds on
top of ADR-0009 inside the AIMS PDCA cycle. AAP records that
judgment layer; the two layers are complementary.

The per-framework mapping (each AAP ADR's relationship to specific
NIST functions / GAI risk categories / ISO 42001 clauses and
Annex A areas / EU AI Act Articles, plus a reverse index for
readers entering from the framework side) is maintained in
[`docs/policy-mapping/`](docs/policy-mapping/README.md), a
deliberately time-bound directory that decays on framework
revision cycles (yearly to multi-year) rather than on vendor
product release cycles. The ADRs themselves stay framework-neutral.

This is not a compliance attestation. Authoritative interpretation
of NIST AI RMF remains with NIST, of ISO/IEC 42001 with ISO/IEC
and accredited certification bodies, of the EU AI Act with the
European Commission and national competent authorities, and of any
framework's application with qualified legal / compliance counsel
for the deploying organization's jurisdiction.

## Using this as an agent-adoption navigator

This repository is meant to be *walked*, not only read. If you are
deciding whether to put an autonomous agent into a given use case — or
auditing one you already shipped — clone it, point your coding agent at
`AGENTS.md`, and use it as a sounding board:

1. [`docs/quadrants/decision-tree.md`](docs/quadrants/decision-tree.md) —
   a five-question triage that routes the work to one of the four Quadrants.
2. [`docs/quadrants/governance-mapping.md`](docs/quadrants/governance-mapping.md) —
   the governance requirements for that Quadrant.
3. The relevant ADRs — especially the triage pair (0009 / 0010).
4. The **Phase axis** (ADR-0010) if the work is autonomy-related: design
   phase or operation phase.
5. [`docs/quadrants/anti-patterns.md`](docs/quadrants/anti-patterns.md) —
   a final check against known failure modes.

The ADRs are a starting point for judgment, not a verdict — re-interpret
them against your own context.

## Reading order

1. [`docs/thesis.md`](docs/thesis.md) — *accountability distribution*, the
   one-page argument.
2. [`docs/adr/README.md`](docs/adr/README.md) — index of ADRs.
3. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) —
   the cleanest entry; the audit test at the end is runnable.
4. The seven essays in publication order (links above).
5. [`docs/quadrants/`](docs/quadrants/) — adoption navigator: decision
   tree, governance mapping, case studies, anti-patterns.
6. [`docs/manifesto.md`](docs/manifesto.md) — civilization-scale questions
   the ADRs do not attempt to answer.
7. [`docs/social-consequence.md`](docs/social-consequence.md) — the
   social-consequence layer: why the internal judgments matter beyond
   audit, as upper rationale rather than specification.

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

This extraction was first compiled by Tatsuya Shimomoto ([@shimo4228](https://github.com/shimo4228)) in April 2026. The ten ADRs and the four Business AI Quadrants re-express, in harness-neutral form, architectural decisions and triage judgments that surfaced through implementing and operating [contemplative-agent](https://github.com/shimo4228/contemplative-agent) and through the seven-essay narrative spine published in April–May 2026; three of these judgments — Security by Absence, the Single External Adapter rule, and the Untrusted Content Boundary — were earlier archived as the [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) governance triplet and re-expressed here among ADRs 0001–0008, while ADR-0009 and the Quadrants navigator emerged from the 2026-04-29 / 04-30 architectural follow-up essays, and ADR-0010 from the 2026-05-01 phase-distinction essay together with the 2026-05-02 skill-design gradient essay.

## Related publication

Two companion position papers distil the narrative spine into harness-neutral statements — the first covers the architectural follow-up (essays 4–7), the second the foundational trilogy (essays 1–3):

> Shimomoto, T. (2026). *Distributing Accountability, Not Capability: Phase Separation and the LLM Workflow Quadrant in Autonomous AI Agent Architectures.* Zenodo. https://doi.org/10.5281/zenodo.20353789

> Shimomoto, T. (2026). *The Two-Layer Black Box: Operator Visibility, Commercial Secrecy, and a Minimum Disclosure Set for Accountable Autonomous AI Agents.* Zenodo. https://doi.org/10.5281/zenodo.20355907

Open access (CC BY 4.0). The concept DOIs above always resolve to the latest version. Both papers are also cross-posted on SSRN: [Distributing Accountability, Not Capability](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6817598) · [The Two-Layer Black Box](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6823878).

## How to Cite

If you use or reference these architectural decision records, please cite:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.20361360},
  url          = {https://doi.org/10.5281/zenodo.20361360},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (two experimental), paired with four Business AI Quadrants as the diagnostic frame and a Phase / Quadrant two-axis structure}
}
```

Or in text:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.20361360

## License

MIT
