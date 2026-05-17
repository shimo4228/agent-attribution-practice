Language: English | [日本語](README.ja.md)

# AI governance framework mapping

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
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

EU AI Act and OECD AI Principles mappings are deferred to a later
release (their decay cadences and granularity differ enough from
NIST/ISO that bundling them in one release would create churn).

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
