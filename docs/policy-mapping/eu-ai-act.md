Language: English | [日本語](eu-ai-act.ja.md)

# EU AI Act — mapping to AAP ADRs

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-31
**Framework version:** Regulation (EU) 2024/1689 (Artificial
Intelligence Act), in force 2024-08-01. Read against the
application-date schedule current at time of writing: prohibited
practices (Art 5) applicable 2025-02-02; general-purpose AI model
obligations applicable 2025-08-02; Annex III high-risk obligations
applicable 2026-08-02; Annex I (product-embedded) high-risk
obligations applicable 2027-08-02.
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** at each delegated/implementing act,
harmonised standard, or Commission guideline that changes how an
Article is read; otherwise yearly. Faster decay than NIST/ISO is
expected through the 2026–2027 phase-in.

> **This is AAP's reading.** Each per-ADR mapping below records
> how AAP reads the relationship between one of its ADRs and one
> or more Articles of the EU AI Act. It is not a compliance
> attestation: AAP does not certify that adopting an ADR satisfies
> an Article, nor that adopting an Article satisfies an ADR. It is
> not legal advice. The authoritative interpretation of the AI Act
> remains with the European Commission, the AI Office, the
> national competent authorities, and qualified legal / compliance
> counsel for the deploying organization's jurisdiction. The
> mapping is offered as a citation surface for cross-reference, not
> as a substitute for the regulation text itself.

## Purpose

The EU AI Act is a *risk-tiered obligation regime*. It sorts AI
systems into four bands — **unacceptable risk** (prohibited
practices, Art 5), **high risk** (the Chapter III obligation stack,
Art 8–15 for providers plus deployer duties in Art 26–27),
**limited risk** (transparency duties, Art 50), and **minimal
risk** (no mandatory obligation) — and attaches obligations to the
band, not to the capability in the abstract.

The Act ships the *obligation*. AAP records the *judgment* — what
to constrain, where the constraint should live, and who bears the
consequences when it fails. The two layers are complementary: the
Act tells a deploying organization which obligations attach to a
high-risk autonomous system; AAP records structural answers to a
specific subset of those obligations, grounded in autonomous-agent
operation, that an organization can adopt to discharge them.

Pattern phrase used throughout this file:

> **The AI Act ships the obligation. AAP records the judgment of
> when, why, and at what cost the obligation is structurally
> discharged** — the Act states the duty (human oversight,
> record-keeping, robustness) and leaves the mechanism to the
> provider/deployer; AAP populates that mechanism choice for the
> autonomous-agent subset.

### A note on the two graduated structures

The Act's four risk tiers and AAP's three-tier
*prohibition-strength hierarchy* (ADR-0001 → 0002 → 0003) are both
graduated, but they grade **different things** and must not be
collapsed into each other:

- The Act's tiers grade *how dangerous a whole system is*
  (unacceptable → minimal), which determines *which obligations
  attach*.
- AAP's prohibition-strength hierarchy grades *how strongly a
  single capability is foreclosed* (absence → deterministic
  scaffolding gate → untrusted-content boundary), which is a
  mechanism-strength axis *inside* the discharge of one obligation.

A high-risk system (Act tier) can discharge its Art 15 robustness
obligation using any tier of the AAP hierarchy. The analogy is
that both prefer elimination over mitigation where achievable
(Art 9(2)(a) ranks risk *elimination by design* ahead of
mitigation; ADR-0001 ranks absence ahead of restriction); it is
not an identity.

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
implemented — answerable by `grep`, not by policy review. This maps
most directly to **Art 9 (risk management system)**, whose
paragraph 2(a) ranks *elimination or reduction of risks through
adequate design and development* ahead of mitigation measures, and
to **Art 15 (accuracy, robustness, cybersecurity)**, which an
absent capability discharges by construction — there is no surface
to be made robust. Where removing a capability moves a system out
of an Annex III high-risk use case entirely, ADR-0001 also touches
the **Art 6 / Annex III** classification boundary upstream. The
Act states the obligation to manage and to design out risk; AAP
records the judgment that absence is structurally stronger than
restriction and that the audit test is `grep`-answerable rather
than review-answerable.

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md)
records that when absence cannot be achieved, the next-strongest
prohibition is deterministic enforcement at the scaffolding layer
(PreToolUse hooks, structural quarantine, adapter gates) firing on
100% of matching inputs outside the LLM. This maps to **Art 15**,
whose robustness and cybersecurity duty (including the Art 15(5)
requirement for resilience against attempts to alter use or
behaviour through exploitation of system vulnerabilities) is better
served by a deterministic gate than by a probabilistic one, and to
**Art 14(3)**, which contemplates oversight measures *built into*
the high-risk system by the provider as distinct from measures left
to the deployer to implement. The Act requires consistent
performance and resilience throughout the lifecycle; AAP records the
judgment that prompt-clause / convention-file / constitutional
enforcement complies only 50–80% under normal conditions and less
under adversarial pressure, and that the structurally honest
discharge is a non-LLM gate.

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.md) records that
all accumulated agent state — episode logs, knowledge store,
distilled identity, the agent's own prior outputs — is treated as
untrusted content when read back into a prompt. This maps to
**Art 15(5)**, which names resilience against *data poisoning,
model poisoning, adversarial examples,* and *confidentiality
attacks* as cybersecurity duties, and to **Art 10 (data and data
governance)**, whose quality and provenance requirements extend to
the data a system feeds back to itself. The Act asks for resilience
against manipulation of inputs and training/operational data; AAP
records the judgment that the trust boundary must include the agent
itself — the agent's own distillation output inherits taint from
its untrusted upstream sources — with fail-closed validation on load
and explicit `<untrusted_content>` markers on injection.

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.md) records that
each agent process has at most one external side-effect adapter, so
blast radius is bounded by design rather than observed post-hoc.
This maps to **Art 9 (risk management)** as a design-time
bounding of impact surface, to **Art 12 (record-keeping)** because
a single side-effect surface yields a single, attributable log
stream rather than an entangled multi-adapter trace, and to
**Art 14 (human oversight)** because a bounded surface is one an
overseeing human can actually monitor. The Act permits a provider
to choose its risk-management measures; AAP records the judgment
that bounding-at-design preserves redirect (multiple agents, each
with one adapter, each with one accountability chain) while
observe-at-runtime requires post-hoc untangling of which adapter
caused which side effect.

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.md) records that every
command modifying an agent's skills, rules, identity, or
constitution requires explicit approval from a named human before
writing, with no `--auto` flag. This is the load-bearing AAP
mapping to **Art 14 (human oversight)** — specifically the Art 14(4)
abilities that oversight must enable: to *correctly interpret*
output (14(4)(c)), to *decide not to use or otherwise disregard,
override or reverse* it (14(4)(d)), and to *intervene or interrupt
through a "stop" button or similar procedure* (14(4)(e)). A
synchronous pre-write gate is the agent-state analogue of the stop
button: the behaviour-modifying write does not happen until the
human acts. The Act requires that oversight be *effective*; AAP
records the judgment that the gate must be synchronous and
pre-write (advisory confirmation and preview-then-run fail under
stochastic generation), and that it must terminate at a human
rather than a second LLM — a probabilistic gate fails open under
prompt injection, which would make the Art 14 oversight nominal
rather than effective.

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.md) records that after
an incident an operator can reconstruct which inputs reached the
agent, which scaffolding version was active, which approvals gated
that version, which external surface was touched, and what the
final output was. This maps strongly to **Art 12 (record-keeping)**,
which requires high-risk systems to *automatically record events
(logs) over their lifetime*, to **Art 19**, which requires
providers to keep those automatically generated logs, and to
**Art 13 (transparency and provision of information to deployers)**,
which presupposes that the system's operation is legible enough to
report. The Act states the logging duty; AAP records the judgment
that the reconstructible form must be build-time structural
(append-only logs, version-pinned scaffolding, audit trails from the
ADR-0005 gate), because runtime observability — telemetry, metrics,
alerts — is necessary but insufficient for autonomous-agent
reconstruction.

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.md) records that
every non-weights component of the agent — system prompts, personas,
rules, identity, tool definitions, RAG indices, the agent loop,
safety gates, runtime harness — is scaffolding, materialized as
files under version control and changeable only through the
ADR-0005 gate. This maps to **Art 11 / Annex IV (technical
documentation)**, which requires a description of the system's
design, components, and control logic, to **Art 13 (transparency)**,
and to **Art 14(4)(a)**, which requires that the overseeing human
be able to *understand the relevant capacities and limitations* of
the system. Behaviour that lives in inspectable files is documentable
and understandable; behaviour internalized into weights is not. The
Act asks for technical documentation; AAP records the judgment that
documentation alone is insufficient — behaviour must *live in files*,
not in the description of files, because weights-internalized
behaviour (via RLHF / Constitutional AI / instruction tuning) moves
out of the accountable, documentable layer entirely.

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.md) records that each
agent process is bound at deployment time to exactly one
identifiable human who is its accountable operator. This maps to
**Art 14**, which frames human oversight as carried out *by natural
persons*, and to **Art 26(2)**, which requires the deployer to
*assign human oversight to natural persons who have the necessary
competence, training and authority, as well as the necessary
support*. The Act requires that named natural persons hold the
oversight role; AAP records the judgment that the endpoint of the
chain must be one named human per agent process (rotation is
compatible if formal, with explicit logged handoffs), not a team or
shared role, because shared accountability under autonomous agents
reproduces the moral-crumple-zone failure. Status: **experimental**
— the binding pattern is structurally honest but has not yet been
validated across diverse organizational settings.

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.md) records that
before adopting an autonomous-loop architecture, the work is triaged
along the four Business AI Quadrants; the autonomous loop is
reserved for the Autonomous Agentic Loop Quadrant, and choosing it
commits the deploying organization to absorb the attribution gap as
a pre-deployment cost by naming an identifiable gap-bearer. This
maps to the **Art 6 / Annex III** classification step (deciding
whether a use is high-risk is itself the first triage), to **Art 9**
(the risk-management system is *established, implemented, documented
and maintained* before and across deployment, not discovered
post-incident), and to **Art 27 (fundamental rights impact
assessment)**, a deployer-side pre-deployment assessment for certain
high-risk uses. The Act requires a pre-deployment classification and
risk-assessment posture; AAP records the judgment that the
deployment decision for autonomous loops carries a structural
attribution gap and that the gap must be borne by a named subject at
deployment time. A scholarly reading of the Act reaches a converging
conclusion that *high-risk agentic systems with untraceable
behavioural drift cannot currently satisfy the Act's essential
requirements* (Nannini et al., arXiv:2604.04604) — the triage exists
because, for the Autonomous Agentic Loop Quadrant, that gap is
structural rather than incidental. Status: **experimental**.

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.md) records that
operation-phase placement of an Autonomous Agentic Loop Quadrant
component requires an explicit *Phase-crossing decision* in addition
to ADR-0009's gap-bearer naming: when a new pattern surfaces in
operation, will the autonomous loop handle it dynamically in place,
or route it back to design as feedback? Both answers admissible,
"we'll figure it out" is not. This maps to **Art 9**, which frames
risk management as a *continuous iterative process run throughout
the entire lifecycle*, to **Art 17 (quality management system)**,
which spans design and operational governance, and to **Art 72
(post-market monitoring)**, the Act's explicit operation-phase
feedback obligation — the channel through which a deployed system's
emergent behaviour is meant to flow back to the provider. The Act
does not name design and operation as phases with inverted
optimization axes; AAP records the judgment that the distinction is
load-bearing for autonomous loops and that the placement requires a
recorded deployment-time decision rather than a deferral. Status:
**experimental**.

## Per-Quadrant note

AAP's four Business AI Quadrants are problem-space categories, not
agent categories. The Act does not categorize AI work along these
axes; it classifies by risk tier and use case. But the AAP
quadrant-to-ADR matrix
([`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md))
maps cleanly onto the Act's high-risk obligation stack:

- **Quadrant 1 (Script)** and **Quadrant 2 (Algorithmic Search)** —
  out of scope for AAP; whether they are high-risk under the Act
  depends on use case, not on these quadrants, and no AAP mapping is
  offered from this file.
- **Quadrant 3 (LLM Workflow)** — six ADRs apply (0001, 0003, 0004,
  0005, 0006, 0007). When such a workflow is deployed in an Annex III
  high-risk use, these populate the Art 9–15 provider stack and the
  Art 14 oversight duty at *medium* control density.
- **Quadrant 4 (Autonomous Agentic Loop)** — all ten ADRs apply.
  ADR-0008, ADR-0009, ADR-0010 are load-bearing here, most strongly
  against **Art 14** (oversight by a named natural person), **Art 9**
  (lifecycle risk management), and **Art 72** (post-market
  monitoring). The same Article 14 obligation receives a very
  different AAP-side instantiation in Quadrant 3 (role-named LLM
  calls, deterministic control flow) than in Quadrant 4 (pre-named
  gap-bearer + Phase-crossing decision).

## Reverse index — EU AI Act Article → applicable ADR(s)

| EU AI Act Article (and subject) | Applicable AAP ADR(s) |
|---|---|
| **Art 5** — prohibited practices (unacceptable risk) | *(tier boundary; no ADR claims to discharge a prohibition — see "two graduated structures")* |
| **Art 6 / Annex III** — high-risk classification | 0001, 0009 |
| **Art 9** — risk management system | 0001, 0004, 0009, 0010 |
| **Art 10** — data and data governance | 0003 |
| **Art 11 / Annex IV** — technical documentation | 0007 |
| **Art 12** — record-keeping (logging) | 0004, 0006 |
| **Art 13** — transparency to deployers | 0006, 0007 |
| **Art 14** — human oversight | 0002, 0004, 0005, 0007, 0008 |
| **Art 15** — accuracy, robustness, cybersecurity | 0001, 0002, 0003 |
| **Art 17** — quality management system | 0010 |
| **Art 19** — retention of automatically generated logs | 0006 |
| **Art 26** — deployer obligations (incl. 26(2) named overseers) | 0008 |
| **Art 27** — fundamental rights impact assessment | 0009 |
| **Art 72** — post-market monitoring | 0010 |

The reverse-index granularity is intentionally coarse (Article
level, occasionally a named paragraph where the Act's wording is
load-bearing — e.g. Art 9(2)(a), Art 14(4)(e), Art 15(5), Art 26(2)).
Delegated acts, implementing acts, and harmonised standards will
refine how each Article is read without renumbering the Articles
themselves; when they do, only this file changes and the per-ADR
mapping above remains stable.

## Secondary scholarship and external convergence

The Act has a fast-growing secondary literature; this file is not a
review of it. Three 2026 sources are noted only because they
independently arrive at — or supply mechanism for — judgments AAP
records from implementation. They are *not* part of the AAP ADR
lineage (the ADRs were extracted from `contemplative-agent`
operation, not from these papers); they are external citation
surface, listed here in the spirit of the non-attestation
disclaimer.

- **Nannini et al., *AI Agents Under EU Law* (arXiv:2604.04604,
  2026-04).** A legal-scholarship mapping of agentic systems onto
  the Act. Its conclusion that *high-risk agentic systems with
  untraceable behavioural drift cannot currently satisfy the Act's
  essential requirements* converges with **ADR-0006** (causal
  traceability) and **ADR-0009** (triage before autonomy): both
  treat untraceability as disqualifying rather than as a tunable
  defect.
- **Safin & Balta, *Autonomy and Agency in Agentic AI:
  Architectural Tactics for Regulated Contexts* (arXiv:2605.12105,
  2026-05).** Proposes architectural tactics — among them
  *checkpoints* and *write staging* (converting an irreversible
  action into a reversible proposal that a human confirms before it
  commits) — for discharging oversight duties. These are *mechanism*
  that an organization could use to instantiate **ADR-0005** under
  Art 14. Consistent with this repository's thesis (*implementation
  dissolves; judgment persists*), the tactic belongs in this
  mechanism-mapping layer, not in the ADR body: the ADR records the
  judgment (a human approves behaviour-modifying writes before they
  land); write-staging is one shipping implementation of it, and
  implementations decay.
- **Bhattarai & Vu, *Trustworthy Agentic AI Requires Deterministic
  Architectural Boundaries* (arXiv:2602.09947, 2026-02).** Argues
  that *deterministic, architectural enforcement, not probabilistic
  learned behaviour* is necessary for trustworthy agentic AI, and
  names a "lethal trifecta" of untrusted input + privileged data
  access + external action capability. This converges with
  **ADR-0002** (deterministic prohibition at the scaffolding layer)
  and **ADR-0003** (untrusted-content boundary) reached
  independently from operation.

## Caveat

The EU AI Act is in active phase-in through 2026–2027, with
delegated acts, implementing acts, Commission guidelines, and
harmonised standards still arriving. Article wording is stable;
*how each Article is read* is not yet settled. This mapping will be
re-checked on each such instrument and at least yearly. Mapping
shifts do not propagate into the ADRs themselves. The ADRs and this
file decay on independent cadences; the ADRs are the durable anchor.
