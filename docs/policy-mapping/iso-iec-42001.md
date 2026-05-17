Language: English | [日本語](iso-iec-42001.ja.md)

# ISO/IEC 42001 AI Management System — mapping to AAP ADRs

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
**Framework version:** ISO/IEC 42001:2023 (first edition,
published 2023-12).
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** multi-year (formal ISO revision cycle,
typically 5 years between editions; amendments may appear sooner).

> **This is AAP's reading.** Each per-ADR mapping below records
> how AAP reads the relationship between one of its ADRs and one
> or more clauses / Annex A controls of ISO/IEC 42001:2023. It is
> not a compliance attestation: AAP does not certify that adopting
> an ADR satisfies an ISO 42001 control, nor that adopting an ISO
> 42001 control satisfies an ADR. It is not legal advice. The
> authoritative interpretation of ISO/IEC 42001 remains with
> ISO/IEC, with accredited certification bodies, and with
> qualified compliance counsel. The mapping is offered as a
> citation surface for cross-reference, not as a substitute for
> the standard text itself.

## Purpose

ISO/IEC 42001:2023 specifies requirements for an **AI Management
System (AIMS)** — an organizational system for governing the
development, deployment, and use of AI, structured around the
Annex SL high-level structure shared with ISO/IEC 27001
(information security), ISO 9001 (quality), ISO 14001
(environment), and other management-system standards. The body
clauses (4–10) cover context, leadership, planning, support,
operation, performance evaluation, and improvement. Annex A
provides normative controls and control objectives that an
organization implements to support the AIMS.

ISO 42001 ships the *management-system shape* — what processes,
roles, documentation, and improvement loops an organization must
have in place to govern AI. AAP records the *judgment layer* that
populates those processes for the autonomous-agent subset — what
to constrain, where the constraint should live, and who bears
the consequences when it fails. The two layers are complementary:
ISO 42001 tells you what management-system pieces must exist;
AAP records what those pieces should contain when the AI is an
autonomous agent.

Pattern phrase used throughout this file:

> **ISO 42001 ships the management-system shape. AAP records the
> judgment of what fills that shape** — the standard requires the
> organization to operate the AIMS, and AAP populates the
> autonomous-agent decisions inside it.

For context outside this file see
[`../adr/README.md`](../adr/README.md) (ADR index),
[`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md)
(per-quadrant control density), and
[`./README.md`](README.md) (directory-level conventions,
non-attestation disclaimer, revision procedure, sibling
relationship to [`../industry-mapping.md`](../industry-mapping.md)).

## PDCA × Phase Separation

ISO 42001, like all Annex SL management-system standards, is
built on the **Plan-Do-Check-Act (PDCA) cycle**: organizations
plan the AIMS, do the planned activities, check performance
against objectives, and act on improvements. PDCA is recursive
and iterative — each turn of the cycle refines the system.

AAP's Phase axis (introduced in
[ADR-0010](../adr/0010-phase-separation.md), 2026-05-01) is a
related but distinct cut. Where PDCA assumes that *flexibility*
(planning, doing new things, learning) and *predictability*
(checking against established objectives) coexist within each
cycle iteration, AAP's design / operation Phase distinction
records that for autonomous-loop placements the two optimization
axes **invert** between phases:

- **Design phase** — path unknown, exploration required, optimization
  axis = flexibility. Includes prototyping, requirements analysis,
  and each session of a coding agent or Deep Research tool.
- **Operation phase** — path fixed, predictability required,
  optimization axis = predictability (audit-tracing, attribution,
  cost stability, SLA conformance).

ADR-0010's load-bearing rule — operation-phase placement of an
Autonomous Agentic Loop Quadrant component requires a recorded
*Phase-crossing decision* in addition to ADR-0009's gap-bearer
naming — sits inside PDCA as a constraint on what kind of "Do"
phase can be implemented for autonomous loops. PDCA does not
itself surface this constraint; AAP records the judgment that
operation-phase autonomous loops carry a structural cost the
generic PDCA cycle does not name.

The PDCA structure and the AAP Phase axis are therefore
complementary: the AIMS PDCA cycle is the *managerial* cadence
the organization runs at; the AAP Phase axis is the *deployment-time
judgment* about which Quadrant-Phase combinations are admissible
inside that cadence.

## Per-ADR mapping

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.md) records that the
strongest prohibition is one where the capability is never
implemented. This maps into ISO 42001 clause 6 (Planning,
including AI risk assessment and AI risk treatment) and Annex A
controls related to **AI system life cycle** — the capability
surface of the AI is bounded at design time, before deployment.
The standard requires that the organization define and document
AI system capabilities; AAP records the judgment that the
strongest treatment for capabilities the organization does not
want is removing them from the codebase entirely, with the audit
test being `grep`-answerable. ISO 42001 ships the requirement
that capability scope be governed; AAP records that *not
implementing* is structurally distinct from *restricting at
runtime* and is the upper bound of treatment strength.

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md)
records that prohibitions that cannot be achieved by absence are
enforced at the scaffolding layer deterministically, outside the
LLM, firing on 100% of matching inputs. This maps into ISO 42001
clause 8 (Operation — operational controls) and Annex A controls
on **AI system life cycle** and **use of AI systems**. The
standard requires operational controls for AI risk treatment;
AAP records the judgment that prompt-clause / convention-file /
constitutional enforcement complies only 50–80% under normal
conditions and less under adversarial pressure, so the
structurally honest operational control is a non-LLM gate.

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.md) records
that all accumulated agent state — including content the agent
itself authored — is treated as untrusted when read back into a
prompt. This maps into ISO 42001 Annex A controls on **data for
AI systems** (data provenance and quality) and on **AI system
life cycle** (handling of the AI's own internal state across
its lifecycle). The standard requires the organization to manage
data quality and provenance for AI systems; AAP records the
judgment that the data-quality boundary must include the agent's
own distillation output (a value-chain component whose taint
inherits from upstream untrusted sources) with fail-closed
validation on load and explicit `<untrusted_content>` markers on
injection.

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.md) records that
each agent process has at most one external side-effect adapter,
bounding blast radius by design. This maps into ISO 42001 clause
6 (Planning — risk treatment) and Annex A controls on **AI
system life cycle** (system design choices) and **third-party
and customer relationships** (the adapter is typically the
interface to an external system). The standard requires that
risk treatment options be considered; AAP records the judgment
that bounding-at-design preserves redirect (multiple agents,
each with one adapter, each with one accountability chain) while
observe-at-runtime requires post-hoc untangling of which adapter
caused which side effect.

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.md) records that
behavior-modifying writes require named human sign-off before
execution. This maps strongly into ISO 42001 clause 5
(Leadership — roles, responsibilities, and authorities) and
Annex A controls on **internal organization** (roles related to
AI) and **AI system life cycle** (change control). The standard
requires that AI-related responsibilities be assigned and
documented; AAP records the judgment that the approval gate must
be synchronous and pre-write, that advisory confirmation and
preview-then-run fail under stochastic generation, and that the
gate terminates at a human rather than at a second LLM.

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.md) records that after
an incident an operator can reconstruct what happened — inputs,
scaffolding version, approval audit trail, external surface,
final output. This maps into ISO 42001 clauses 9 (Performance
evaluation, including monitoring, measurement, analysis, and
internal audit) and 10 (Improvement, including nonconformity and
corrective action). The standard requires that AIMS performance
be measurable and that corrective action follow nonconformities;
AAP records the judgment that *post-incident reconstruction is a
build-time structural requirement, not a runtime observability
practice* — runtime observability (metrics, tracing, alerts) is
necessary but insufficient for autonomous-agent reconstruction,
and append-only episode logs plus version-pinned scaffolding plus
ADR-0005 audit trails are the substrate that makes reconstruction
possible.

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.md) records that
every non-weights component of the agent lives in operator-visible
files, not in opaque weights. This maps into ISO 42001 clause 7
(Support — including documented information requirements) and
Annex A controls on **AI system life cycle** (configuration and
change management) and **information for interested parties of
AI systems** (transparency). The standard requires that the
AIMS maintain documented information about its AI systems; AAP
records the judgment that documentation alone is insufficient —
behavior must *live in files*, not in the description of files,
because weights-internalization (RLHF, Constitutional AI,
instruction tuning) moves behavior out of the accountable layer
entirely and makes the documentation no longer the ground truth.

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.md) records that each
agent process is bound at deployment to exactly one identifiable
human as accountable operator. This maps into ISO 42001 clause 5
(Leadership — assignment of roles, responsibilities, authorities)
and Annex A controls on **internal organization**. The standard
requires that responsibilities for AI systems be assigned and
documented; AAP records the judgment that the endpoint of the
accountability chain must be one named human per agent process
(rotation is compatible if formal, with explicit logged handoffs),
not a team or shared role, because shared accountability under
autonomous agents reproduces the moral-crumple-zone failure.
Status: **experimental**.

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.md) records that
before adopting an autonomous-loop architecture the work is
triaged along the four Business AI Quadrants, and that choosing
the Autonomous Agentic Loop Quadrant commits the deploying
organization to absorb the attribution gap as a pre-deployment
cost by naming an identifiable gap-bearer at deployment time.
This maps into ISO 42001 clauses 4 (Context of the organization,
including determining the scope of the AIMS), 6 (Planning,
including AI risk assessment), and Annex A controls on
**assessing impacts of AI systems** and **AI system life cycle**
(deployment decisions). The standard requires that the
organization assess impacts and make informed deployment
decisions; AAP records the judgment that the deployment decision
for autonomous loops carries a *structural attribution gap* that
must be borne by a named subject at deployment time, not
discovered post-incident. Status: **experimental**.

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.md) records that
operation-phase placement of an Autonomous Agentic Loop Quadrant
component requires an explicit *Phase-crossing decision* in
addition to ADR-0009's gap-bearer naming. This maps into ISO
42001 clauses 6 (Planning — risk and opportunity treatment) and
8 (Operation — operational planning and control) and Annex A
controls on **AI system life cycle** (lifecycle management).
The PDCA cycle around the AIMS does not by itself distinguish
design from operation as phases with inverted optimization axes;
AAP records the judgment that for autonomous loops the
distinction is load-bearing and the placement requires a
recorded deployment-time decision (handle new operation-phase
patterns dynamically in place, or route them back to design as
feedback). Status: **experimental**.

## Per-Quadrant note

AAP's four Business AI Quadrants are problem-space categories,
not agent categories. ISO 42001 does not categorize AI work along
these axes, but the AAP quadrant-to-ADR matrix
([`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md))
maps cleanly into ISO 42001's structure:

- **Quadrant 1 (Script)** and **Quadrant 2 (Algorithmic Search)** —
  out of scope for AAP, no ISO 42001 mapping required from this
  file.
- **Quadrant 3 (LLM Workflow)** — six ADRs apply (0001, 0003,
  0004, 0005, 0006, 0007), populating ISO 42001's PDCA cycle
  with the *medium-governance* control density. The AIMS
  improvement loop runs at component granularity.
- **Quadrant 4 (Autonomous Agentic Loop)** — all ten ADRs apply,
  populating ISO 42001's PDCA cycle with the *high-governance*
  control density. ADR-0008, ADR-0009, ADR-0010 are load-bearing
  here, most strongly under ISO 42001 clauses 5 (Leadership) and
  6 (Planning). The improvement loop runs at architectural rather
  than component granularity because component-level redirect is
  foreclosed (see
  [`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md)).

The same Annex A control receives different AAP-side instantiation
depending on Quadrant: an "internal organization" control is
satisfied in Quadrant 3 by named LLM-call roles, in Quadrant 4 by
the pre-named gap-bearer.

## Reverse index — ISO 42001 area → applicable ADR(s)

| ISO 42001 area | Applicable AAP ADR(s) |
|---|---|
| Clause 4 — Context of the organization | 0009 |
| Clause 5 — Leadership (roles, responsibilities, authorities) | 0005, 0008 |
| Clause 6 — Planning (risk assessment, risk treatment) | 0001, 0004, 0009, 0010 |
| Clause 7 — Support (documented information) | 0007 |
| Clause 8 — Operation (operational controls) | 0002, 0010 |
| Clause 9 — Performance evaluation (monitoring, audit) | 0006 |
| Clause 10 — Improvement (corrective action) | 0006 |
| Annex A — Policies related to AI | 0007, 0009 |
| Annex A — Internal organization (roles for AI) | 0005, 0008 |
| Annex A — AI system life cycle | 0001, 0002, 0003, 0004, 0006, 0007, 0010 |
| Annex A — Data for AI systems | 0003 |
| Annex A — Information for interested parties of AI systems | 0007 |
| Annex A — Use of AI systems | 0002, 0005 |
| Annex A — Third-party and customer relationships | 0004 |
| Annex A — Assessing impacts of AI systems | 0009 |

The reverse-index granularity is intentionally coarse (clause
level and Annex A area level, not individual control number):
ISO 42001 Annex A control numbering may be reorganized in future
editions, but AAP-side judgments do not shift on renumbering.
When ISO revises Annex A organization, only this file changes;
the per-ADR mapping above remains stable.

## Caveat

ISO/IEC 42001:2023 is the first edition of the standard. A
revised edition or amendments may reorganize Annex A controls or
adjust body-clause requirements. The mapping above will be
re-checked on each ISO revision and amendment. Mapping shifts do
not propagate into the ADRs themselves. The ADRs and this file
decay on independent cadences; the ADRs are the durable anchor.
