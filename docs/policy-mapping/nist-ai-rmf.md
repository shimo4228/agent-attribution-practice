Language: English | [日本語](nist-ai-rmf.ja.md)

# NIST AI Risk Management Framework — mapping to AAP ADRs

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
**Framework version:** NIST AI Risk Management Framework 1.0
(NIST.AI.100-1, 2023-01) + Generative AI Profile (NIST.AI.600-1,
2024-07).
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** yearly check (GAI Profile expected to
revise more often than the core RMF).

> **This is AAP's reading.** Each per-ADR mapping below records
> how AAP reads the relationship between one of its ADRs and one
> or more functions / categories of NIST AI RMF (and, where
> applicable, risk categories of the Generative AI Profile). It
> is not a compliance attestation: AAP does not certify that
> adopting an ADR satisfies a NIST function, nor that adopting a
> NIST function satisfies an ADR. It is not legal advice. The
> authoritative interpretation of NIST AI RMF remains with NIST
> and with qualified compliance counsel. The mapping is offered as
> a citation surface for cross-reference, not as a substitute for
> the framework text itself.

## Purpose

NIST AI RMF 1.0 organizes AI risk management around four
functions — **GOVERN, MAP, MEASURE, MANAGE** — each with categories
and subcategories that an organization populates with its own
controls. The Generative AI Profile (NIST.AI.600-1) adds risk
categories specific to generative and dual-use AI systems
(confabulation, information integrity, human-AI configuration,
value chain and component integration, etc.) and references RMF functions
throughout.

NIST RMF ships the *structure* — what to govern, what to map,
what to measure, what to manage. AAP records the *judgment* —
what to constrain, where the constraint should live, and who
bears the consequences when it fails. The two layers are
complementary: NIST tells you which questions to organize around;
AAP records answers to a specific subset of those questions
grounded in autonomous-agent operation.

Pattern phrase used throughout this file:

> **NIST RMF ships X. AAP records the judgment of when, why, and
> at what cost X should be enforced** — the framework leaves the
> specific control choice to organizations, and AAP populates that
> choice for the autonomous-agent subset.

For context outside this file see
[`../adr/README.md`](../adr/README.md) (ADR index),
[`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md)
(per-quadrant control density), and
[`./README.md`](README.md) (directory-level conventions,
non-attestation disclaimer, revision procedure, sibling
relationship to [`../industry-mapping.md`](../industry-mapping.md)).

## Per-ADR mapping

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.md) records that the
strongest prohibition is one where the capability is never
implemented — answerable by `grep`, not by policy review. This
maps most directly into the **GOVERN** function (organizational
posture toward AI capability scope) and **MAP** function (system
boundary documentation): the system's capability surface is
defined by what the harness ships, not by runtime controls.
Under the Generative AI Profile, ADR-0001 contributes to risk
treatment for **Dangerous, Violent, or Hateful Content** and
**Information Security** risk categories by removing the
capability surface where the risk would otherwise need to be
managed. NIST RMF ships the question of *whether the
organization governs capability scope*; AAP records the
judgment that absence is structurally stronger than restriction
and that the audit test is `grep`-answerable.

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md)
records that when absence cannot be achieved, the next-strongest
prohibition is deterministic enforcement at the scaffolding layer
(PreToolUse hooks, structural quarantine, adapter gates) firing
on 100% of matching inputs outside the LLM. This maps into the
**MANAGE** function (specifically the categories around risk
treatment and risk response): the enforcement mechanism that
implements the management decision is required to be deterministic
rather than probabilistic. Under the Generative AI Profile,
ADR-0002 strengthens treatment for **Information Security**
(prompt-injection resistance) and **Confabulation** (preventing
unauthorized writes that confabulated reasoning could otherwise
trigger). NIST RMF allows the organization to choose its
enforcement mechanism; AAP records the judgment that
prompt-clause / convention-file / constitutional enforcement
complies only 50–80% under normal conditions and less under
adversarial pressure, and that the structurally honest treatment
is a non-LLM gate.

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.md) records that
all accumulated agent state — episode logs, knowledge store,
distilled identity, the agent's own prior outputs — is treated as
untrusted content when read back into a prompt. This maps into
the **MAP** function (data flow and trust boundary documentation)
and the **MANAGE** function (treatment of risks from accumulated
state). Under the Generative AI Profile, ADR-0003 addresses the
**Data Privacy**, **Information Integrity**, and **Value Chain
and Component Integration** risk categories — the agent's own
distillation output is a value-chain component whose taint
inherits from its untrusted upstream sources. NIST RMF asks the
organization to identify trust boundaries; AAP records the
judgment that the boundary must include the agent itself, with
fail-closed validation on load and explicit `<untrusted_content>`
markers on injection.

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.md) records that
each agent process has at most one external side-effect adapter,
so blast radius is bounded by design rather than observed
post-hoc. This maps into the **GOVERN** function (system
architecture governance, decommissioning planning) and the
**MAP** function (impact categorization). Under the Generative AI
Profile, ADR-0004 reduces exposure across **Information
Security**, **Value Chain and Component Integration**, and
**Human-AI Configuration** risk categories simultaneously by
collapsing the surface count at design time. NIST RMF allows
either *bound at design* or *observe at runtime* as valid risk
treatments; AAP records the judgment that bounding-at-design
preserves redirect (multiple agents, each with one adapter, each
with one accountability chain) while observe-at-runtime requires
post-hoc untangling of which adapter caused which side effect.

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.md) records that
every command modifying an agent's skills, rules, identity, or
constitution requires explicit approval from a named human
before writing, with no `--auto` flag. This maps directly into
the **GOVERN** function (categories covering roles,
responsibilities, accountability structures) and the **MANAGE**
function (treatment requiring human review). Under the Generative
AI Profile, ADR-0005 addresses the **Human-AI Configuration**
risk category (oversight by named humans for behavior-changing
decisions). NIST RMF requires that human roles in oversight be
established; AAP records the judgment that the gate must be
synchronous and pre-write, that advisory confirmation and
preview-then-run fail under stochastic generation, and that the
gate must terminate at a human rather than at a second LLM (a
second LLM is a probabilistic gate that fails open under prompt
injection).

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.md) records that after
an incident an operator can reconstruct which inputs reached the
agent, which scaffolding version was active, which approvals
gated that version, which external surface was touched, and what
the final output was. This maps strongly into the **MEASURE**
function (mechanisms for AI risk tracking, post-incident analysis)
and the **MANAGE** function (incident response). Under the
Generative AI Profile, ADR-0006 supports **Information Security**
and **Information Integrity** risk categories by making each
event reconstructible after the fact. NIST RMF allows the
organization to choose whether reconstruction capability is
runtime observability (telemetry, metrics, alerts) or build-time
structural (append-only logs, version-pinned scaffolding, audit
trails from the ADR-0005 gate); AAP records the judgment that the
structural form is required because runtime observability is
necessary but insufficient for autonomous-agent reconstruction.

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.md) records that
every non-weights component of the agent — system prompts,
personas, rules, identity, tool definitions, RAG indices, the
agent loop, safety gates, runtime harness — is scaffolding,
materialized as files under version control, inspectable by the
operator, and changeable only through the ADR-0005 approval gate.
This maps into the **GOVERN** function (system documentation,
change management) and the **MEASURE** function (auditability of
the system that risk measurement applies to). Under the Generative
AI Profile, ADR-0007 contributes to **Information Integrity** and
**Human-AI Configuration** by keeping behavior in the accountable
layer rather than internalizing it into model weights via RLHF /
Constitutional AI / instruction tuning. NIST RMF asks for system
documentation; AAP records the judgment that documentation alone
is insufficient — behavior must *live in files*, not in the
description of files, because weights-internalized behavior moves
out of the accountable layer entirely.

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.md) records that each
agent process is bound at deployment time to exactly one
identifiable human who is its accountable operator. This maps
into the **GOVERN** function (categories around roles,
responsibilities, and the accountability chain — typically
GOVERN 2.x). Under the Generative AI Profile, ADR-0008 addresses
the **Human-AI Configuration** risk category by terminating the
oversight chain at a named individual rather than a team / role /
committee. NIST RMF requires that AI actor roles be identified
and accountable; AAP records the judgment that the endpoint of
the chain must be one named human per agent process (rotation is
compatible if formal, with explicit logged handoffs), not a team
or shared role, because shared accountability under autonomous
agents reproduces the moral-crumple-zone failure. Status:
**experimental** — the binding pattern is structurally honest but
has not yet been validated across diverse organizational
settings.

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.md) records that
before adopting an autonomous-loop architecture for a piece of
work, the work is triaged along the four Business AI Quadrants;
the autonomous loop is reserved for the Autonomous Agentic Loop
Quadrant, and choosing it commits the deploying organization to
absorb the attribution gap as a pre-deployment cost by naming an
identifiable gap-bearer. This is the load-bearing AAP
contribution to NIST's **GOVERN** function (organizational
posture toward AI risk) at the deepest layer: NIST asks
organizations to *decide* whether and how to deploy AI; AAP
records the judgment that the deployment decision for autonomous
loops carries a structural attribution gap and that the gap must
be borne by a named subject at deployment time, not discovered
post-incident. Under the Generative AI Profile, ADR-0009 sits at
the deployment-decision layer for generative agents and addresses
**Human-AI Configuration** and **Value Chain and Component
Integration** by recording who answers when component-level
redirect is foreclosed. Status: **experimental**.

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.md) records that
operation-phase placement of an Autonomous Agentic Loop Quadrant
component requires an explicit *Phase-crossing decision* in
addition to ADR-0009's gap-bearer naming — when a new pattern
surfaces in operation, will the autonomous loop handle it
dynamically in place, or route it back to design as feedback?
Both answers admissible, "we'll figure it out" is not. This maps
into the **GOVERN** function (organizational governance over the
AI lifecycle) and the **MANAGE** function (treatment of risks
that emerge during operation). Under the Generative AI Profile,
ADR-0010 addresses **Information Integrity**, **Human-AI
Configuration**, and **Value Chain and Component Integration**
risk categories at the operation / lifecycle boundary — these
risk categories take different shapes in design phase (path
unknown) vs operation phase (path fixed). NIST RMF does not
specifically distinguish design from operation as separate AI
lifecycle phases with inverted optimization axes; AAP records
the judgment that the distinction is load-bearing for autonomous
loops and that the placement requires a recorded deployment-time
decision. Status: **experimental**.

## Per-Quadrant note

AAP's four Business AI Quadrants are problem-space categories, not
agent categories. NIST RMF does not categorize AI work along these
axes, but the AAP quadrant-to-ADR matrix
([`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md))
maps cleanly into NIST's structure:

- **Quadrant 1 (Script)** and **Quadrant 2 (Algorithmic Search)** —
  out of scope for AAP, no NIST mapping required from this file.
- **Quadrant 3 (LLM Workflow)** — six ADRs apply (0001, 0003,
  0004, 0005, 0006, 0007), populating NIST **GOVERN**, **MAP**,
  **MEASURE**, **MANAGE** with the *medium-governance* control
  density.
- **Quadrant 4 (Autonomous Agentic Loop)** — all ten ADRs apply,
  populating NIST functions with the *high-governance* control
  density. ADR-0008, ADR-0009, ADR-0010 are load-bearing here,
  most strongly under NIST **GOVERN** categories on accountability
  structure and incident response.

The Generative AI Profile's *Human-AI Configuration* risk category
is satisfied with very different control density in Quadrant 3
(role-named LLM calls, deterministic control flow) than in
Quadrant 4 (pre-named gap-bearer + Phase-crossing decision); the
same NIST clause receives different AAP-side instantiation
depending on Quadrant.

## Reverse index — NIST function → applicable ADR(s)

| NIST function (and notable categories) | Applicable AAP ADR(s) |
|---|---|
| **GOVERN** — organizational posture, accountability structures, roles, decommissioning | 0001, 0004, 0005, 0007, 0008, 0009, 0010 |
| **MAP** — context, system boundary, data flow, trust boundary | 0001, 0003, 0004 |
| **MEASURE** — risk tracking, auditability, post-incident analysis | 0006, 0007 |
| **MANAGE** — risk treatment, incident response, lifecycle management | 0002, 0003, 0005, 0006, 0010 |

| Generative AI Profile risk category | Applicable AAP ADR(s) |
|---|---|
| Dangerous, Violent, or Hateful Content | 0001 |
| Confabulation | 0002 |
| Data Privacy | 0003 |
| Information Integrity | 0003, 0006, 0007, 0010 |
| Information Security | 0001, 0002, 0006 |
| Human-AI Configuration | 0005, 0007, 0008, 0009, 0010 |
| Value Chain and Component Integration | 0003, 0004, 0009, 0010 |

ADRs not in the indexes above (no GAI Profile risk category aligns
cleanly): ADR-0001 (the GAI Profile addresses dangerous-content
treatment, but absence is upstream of treatment) and the table
above duplicates ADR-0001 only where the alignment is strong;
absence is structurally adjacent to every risk category but does
not *target* any single one.

The reverse-index granularity is intentionally coarse (function
level + risk-category level, not subcategory level): NIST
subcategory numbering varies between RMF revisions and between
the core RMF and the GAI Profile, and AAP-side judgments do not
shift on subcategory renumbering. When NIST revises subcategory
identifiers, only this file changes; the per-ADR mapping above
remains stable.

## Caveat

NIST AI RMF is a living document; the GAI Profile in particular
is expected to revise more often than the core RMF. The mapping
above will be re-checked annually and on each profile revision.
Mapping shifts do not propagate into the ADRs themselves. The
ADRs and this file decay on independent cadences; the ADRs are
the durable anchor.
