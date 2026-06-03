Language: English | [日本語](README.ja.md)

# AI governance framework mapping

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-06-03
**Maintenance posture:** This directory decays. The ADRs do not.

## Purpose

The ADRs in this repository record judgments about how attribution
should be distributed in autonomous AI agent systems. National and
international AI governance frameworks (NIST AI RMF, ISO/IEC 42001,
EU AI Act, OECD AI Principles) ship the *governance framework
layer* — control catalogs, management-system requirements, risk
tiers, principle statements — that those judgments can be read
against. This directory tracks, at the time of writing, how AAP's
ten ADRs and four Business AI Quadrants relate to specific clauses
of each framework.

The mapping is kept in a separate directory, not embedded in the
ADRs themselves, to honour the repository's core thesis:
*implementation dissolves; judgment persists*. Frameworks will
be revised, superseded, re-numbered, or replaced; the ADRs will
not. Editing this directory as frameworks evolve does not require
touching the ADRs.

This is the **governance framework layer** counterpart to the
[*industry mechanism layer mapping*](../industry-mapping.md), which
maps vendor products (Microsoft Agent 365, Agent Governance Toolkit,
Purview, etc.) to ADRs. The two directories are siblings; they
target different reader audiences (policy / compliance officer vs
engineer / architect) and decay at different cadences.

## Files in this directory

| File | Framework | Framework version | Maintenance cadence |
|---|---|---|---|
| [nist-ai-rmf.md](nist-ai-rmf.md) (+ [ja](nist-ai-rmf.ja.md)) | NIST AI Risk Management Framework | RMF 1.0 (2023-01) + Generative AI Profile NIST.AI.600-1 (2024-07) | yearly (GAI Profile expected to revise more often than core RMF) |
| [iso-iec-42001.md](iso-iec-42001.md) (+ [ja](iso-iec-42001.ja.md)) | ISO/IEC 42001 AI Management System | ISO/IEC 42001:2023 | multi-year (formal ISO revision cycle) |
| [eu-ai-act.md](eu-ai-act.md) (+ [ja](eu-ai-act.ja.md)) | EU AI Act (Regulation (EU) 2024/1689) | in force 2024-08-01; phased application through 2027 | per delegated/implementing act, harmonised standard, or Commission guideline; else yearly (faster decay through the 2026–2027 phase-in) |

The OECD AI Principles mapping is deferred to a later release
(its decay cadence and granularity differ enough from the
regulation/standard files that bundling it in would create churn).

## Direction convention

**ADR-centric primary direction.** Each framework file lists ADRs
0001–0010 in order, and notes which clause(s) of that framework
the ADR's judgment instantiates. AAP-side anchors stay stable
across framework revisions.

**Reverse index secondary direction.** Each framework file ends
with a reverse index (framework clause → applicable ADR) so that a
reader coming from the framework side — a NIST control owner, an
ISO 42001 auditor, an EU AI Act compliance officer — has a citation
entry.

## Framework version pinning convention

Each framework file's header pins the **exact framework version /
profile / publication date** it was written against. Example:

> *Framework version: NIST AI Risk Management Framework 1.0
> (NIST.AI.100-1, 2023-01) + Generative AI Profile (NIST.AI.600-1,
> 2024-07).*

When a framework releases a revision, only the file targeting that
framework is updated. The ADRs are not touched. Cross-version
diffs in the framework do not propagate into the ADR bodies under
any circumstance — see *Revision procedure* below.

## Revision procedure

When a framework publishes a revision:

1. Open the framework file (e.g. `nist-ai-rmf.md`) and update its
   header `Framework version:` and `Date:` lines.
2. Walk each per-ADR mapping; revise wording where the new
   framework version moved, renamed, or retired the cited clause.
3. Update the reverse index.
4. Update the Japanese mirror to match.
5. **Do not** touch any file under `docs/adr/`. The ADRs' job is
   to record the judgment, not to track which framework cites
   what this quarter.
6. Commit with a message naming the framework + version
   (e.g. `docs(policy-mapping): refresh against NIST GAI Profile
   2026-Q4 revision`).

If a framework introduces a clause AAP's existing ADRs have no
judgment about, **the ADR side is not extended retroactively** —
either a new ADR captures the judgment from first principles (with
its own Lineage), or the mapping file notes the gap explicitly.
Mapping coverage is not a target.

## Non-attestation disclaimer (template — applies to every file in this directory)

> **This is AAP's reading.** Each per-ADR mapping below records
> how AAP reads the relationship between one of its ADRs and one
> or more clauses of the named framework. It is not a compliance
> attestation: AAP does not certify that adopting an ADR satisfies
> a framework clause, nor that adopting a framework clause
> satisfies an ADR. It is not legal advice. The authoritative
> interpretation of any framework remains with the framework
> body (NIST, ISO/IEC, the European Commission, the OECD) and
> with qualified legal / compliance counsel for the deploying
> organization's jurisdiction. The mapping is offered as a
> citation surface for cross-reference, not as a substitute for
> the framework text itself.

Each framework file repeats this disclaimer in its own header.

## Sibling: industry mechanism layer

[`../industry-mapping.md`](../industry-mapping.md) is the *vendor
product* counterpart of this directory. It maps shipping vendor
mechanisms (sub-millisecond policy gates, agent-identity
primitives, sponsor systems, cross-vendor audit, registry sync) to
ADRs. The two layers populate different parts of the AAP
landscape:

- *Governance framework layer* (this directory) — what regulators,
  standards bodies, and management-system audits cite. Decays on
  framework revision cycles (yearly to multi-year).
- *Industry mechanism layer* — what vendor products ship. Decays
  on product release cycles (quarterly or faster).

A given ADR may have entries in one, both, or neither layer at any
given time. The ADR itself remains the durable anchor.

## Empirical corroboration

The per-framework files above read AAP's judgments against
governance-framework *text*. A separate and weaker question is
whether *field evidence* points independently at the same
judgments. Through 2026, several enterprise surveys converged on a
finding adjacent to AAP's thesis: the dominant cause of AI-agent
production failure is the absence of accountability structure, not
a deficit of model capability. They are listed here as external
citation surface, in the spirit of the non-attestation disclaimer —
they are *not* part of the AAP ADR lineage (the ADRs were extracted
from implementation, not from these surveys), and survey
methodology and numbers decay faster than the framework text the
rest of this directory maps.

- **[Digital Applied, *AI Agent Scaling Gap*, March 2026](https://www.digitalapplied.com/blog/ai-agent-scaling-gap-march-2026-pilot-to-production).**
  A 650-respondent enterprise survey: 78% run at least one agent
  pilot, only 14% reach production scale, and five
  organizational/operational gaps (legacy integration, output
  quality at volume, monitoring absence, unclear ownership, domain
  data) account for 89% of scaling failures. Organizations that
  stood up an operations function *before* any incident saw
  materially fewer production rollbacks. This corroborates the
  design→operation optimization-axis inversion behind **ADR-0010**
  (phase separation) and the *pre-named gap-bearer* commitment
  behind **ADR-0008 / ADR-0009**: the accountable owner is named
  before the failure, not appointed after it.
- **[Grant Thornton, *2026 AI Impact Survey*](https://www.grantthornton.com/insights/press-releases/2026/april/grant-thornton-survey-on-ai-proof-gap).**
  Of nearly 1,000 senior leaders, 78% lack full confidence they
  could pass an independent AI-governance audit within 90 days, only
  11% would prioritize risk and compliance as an AI success factor,
  and among the roughly three-quarters operating autonomous systems
  only about one in five has tested a response plan for AI failures.
  This corroborates **ADR-0009** (triage before autonomy): an
  un-triaged deployment cannot answer the accountability question on
  demand.
- **[CSA, *AI Agent Governance Framework Gap*, 2026](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-governance-framework-gap-20260403/).**
  Of 235 large-enterprise security leaders, 92% are concerned about
  AI-agent security yet most report significant governance gaps, 95%
  doubt they could detect or contain a compromised agent, only 38%
  monitor AI traffic (prompts, tool calls, outputs) end-to-end, and
  only 17% continuously monitor agent-to-agent interaction. This
  corroborates **ADR-0006** (causal traceability): the infrastructure
  to reconstruct *which agent did what, on whose instruction, under
  which credential* is largely absent in the field.

These surveys are convergent, not foundational. AAP reached the same
judgments inductively, from implementation friction; the surveys
arrived at an adjacent conclusion independently and later. Treat the
numbers as a 2026 snapshot that will decay; the ADRs they point at
will not.

## What this directory does *not* claim

- That AAP's ADRs cover every clause of any listed framework. The
  ADRs were extracted from implementation; the frameworks were
  drafted from policy. Coverage is partial in both directions.
- That a framework's clauses fully express the AAP ADR they map
  to. The judgment layer (when, why, at what cost) typically goes
  further than the framework's clause text.
- That the listed frameworks are the only relevant ones, or the
  most authoritative ones for any specific jurisdiction. Selection
  reflects current LLM-mediated citation surface, not a normative
  ranking.

## Caveat

The frameworks listed here will be revised, renumbered,
superseded, or replaced. The ADRs they map to will not. Update
this directory as frameworks evolve; do not back-propagate
framework changes into the ADRs.
