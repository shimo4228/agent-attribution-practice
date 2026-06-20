Language: English | [日本語](singapore-mgf-agentic.ja.md)

# Singapore Model AI Governance Framework for Agentic AI — mapping to AAP ADRs

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-06-12
**Framework version:** IMDA, *Model AI Governance Framework for
Agentic AI*, first released 2026-01-22 (announced at the World
Economic Forum 2026) as the first national governance framework
specific to agentic AI. This mapping is written against **Version
1.5, published 2026-05-20 (updated 2026-06-05)**, which already
incorporates feedback from 60+ companies since v1.0 — including a
new *systemic and multi-agent risks* section (1.2.3) absent from
the launch version.
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** per MGF version (IMDA describes the
framework as *a living document* to be *continuously updated*; the
v1.0 → v1.5 interval was under four months, so expect the fastest
decay in this directory); otherwise yearly.

> **This is AAP's reading.** Each per-ADR mapping below records
> how AAP reads the relationship between one of its ADRs and one
> or more sections of the MGF for Agentic AI. It is not a
> compliance attestation: AAP does not certify that adopting an ADR
> satisfies an MGF practice, nor that adopting an MGF practice
> satisfies an ADR. It is not legal advice. The authoritative
> interpretation of the framework remains with IMDA and with
> qualified legal / compliance counsel for the deploying
> organization's jurisdiction. The mapping is offered as a citation
> surface for cross-reference, not as a substitute for the
> framework text itself.

## Purpose

The MGF for Agentic AI is a *voluntary best-practice catalogue*,
not an obligation regime: compliance is voluntary, while legal
accountability for an agent's behaviour remains with the deploying
organization regardless. It is organized as four dimensions —
**(1) assess and bound the risks upfront** (§2.1), **(2) make
humans meaningfully accountable** (§2.2), **(3) implement technical
controls and processes** (§2.3), **(4) enable end-user
responsibility** (§2.4) — explicitly framed as an iterative
process: an anomaly detected in monitoring sends the organization
back to re-assess and re-bound.

Of the frameworks mapped in this directory, the MGF is the closest
to AAP's subject matter: it is the only one written *specifically
for* autonomous agents, and it names AAP's core problem in its own
words — *"Multiple stakeholders may also be involved in different
parts of the agent lifecycle, diffusing accountability"* (§2.2).
The relationship is therefore denser than with NIST / ISO / the AI
Act, and so is the tension (see *Convergence and tension* below).

Pattern phrase used throughout this file:

> **The MGF ships the practice catalogue. AAP records the judgment
> of when, why, and at what cost a practice is structurally
> sufficient** — the MGF lists emerging best practices and leaves
> the selection to the organization; AAP records which selections
> are load-bearing for attribution and which fail under autonomy.

For context outside this file see
[`../adr/README.md`](../adr/README.md) (ADR index),
[`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md)
(per-quadrant control density), and [`./README.md`](README.md)
(directory-level conventions, non-attestation disclaimer, revision
procedure).

## Per-ADR mapping

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.md) records that the
strongest prohibition is one where the capability is never
implemented. This maps to **§2.1.2 (agent limits)**, whose general
rule — *prefer deterministic rather than non-deterministic limits,
and bound by design*, e.g. *impose access controls that prevent the
tool from being called by the agent at all* rather than prompting
the agent not to use it — is the same elimination-over-mitigation
ordering, and to **§2.1.1**, which opens the framework by noting
that *not all use cases are suitable for agents*. The MGF ships
least-privilege bounding as the practice; AAP records the judgment
that there is a tier above bounding — a tool that does not exist
cannot be un-prevented — and that the audit test for it is
`grep`-answerable rather than configuration-review-answerable.

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.md)
records that when absence cannot be achieved, the next-strongest
prohibition is deterministic enforcement at the scaffolding layer,
firing on 100% of matching inputs outside the LLM. This is the
strongest single convergence in this file: **§2.3.1** instructs
organizations, when selecting controls, to prefer *structural,
rule-based* over *model-based or prompt-layer* safeguards —
*"rather than instructing an agent not to use specific tools,
access controls can be implemented at the tool layer"* — and notes
that prompt-layer safeguards *tend to be inconsistently defined
across users, as opposed to system-level safeguards that can be
consistently defined and enforced*. That is the same probabilistic
vs deterministic distinction ADR-0002 records, reached from
deployment practice rather than from a single agent's operation.
The MGF retains model-based safeguards for risks *harder to define
through fixed rules*; ADR-0002's scope is narrower — prohibitions,
where partial compliance is failure — which is why it admits no
probabilistic tier at all.

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.md) records that
all accumulated agent state — including the agent's own prior
outputs — is treated as untrusted content when read back into a
prompt. This maps to **§2.1.1's threat-modelling note**, which
names *memory poisoning* among common threats and recommends
*taint tracing* — mapping *how untrusted data can move through the
system* — for complex agentic systems, and to **§2.3.1
(multi-agent interactions)**, which recommends structured schemas
over free text between agents and limiting shared memory access.
The MGF treats taint as something to trace through the system; AAP
records the further judgment that the trace terminates at the agent
itself — distillation output inherits taint from its untrusted
upstream sources, so there is no point in the loop where
accumulated state re-enters as trusted.

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.md) records that
each agent process has at most one external side-effect adapter, so
blast radius is bounded by design. This maps to **§2.1.2 (agent
limits)** — least-privilege tool access, and *structuring agents
around functional boundaries* as *a natural constraint* — and to
the framework's own worked example (§2, IMDA case study), which
advises against *a single "all-powerful" agent with unrestricted
access* in favour of *multiple agents with narrow, clearly defined
rules*. The MGF motivates the split by risk bounding; AAP records
the additional attribution motivation: one adapter per process
yields one attributable side-effect stream per accountability
chain, where a multi-adapter agent requires post-hoc untangling of
which surface caused which effect.

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.md) records that every
command modifying an agent's skills, rules, identity, or
constitution requires synchronous pre-write approval from a named
human. This maps to **§2.2.2 (design for meaningful human
oversight)**: define *significant checkpoints or action boundaries
that require human approval* before high-stakes or irreversible
actions; keep approval requests contextual and digestible; and —
the fail-closed clause — *deny action by default when approval
infrastructures fail*. §2.2.2 also supplies the audit posture
ADR-0005 presupposes: monitor human override rates and response
times to detect rubber-stamping, because an approval gate that is
always approved is oversight in name only. The MGF ships the
checkpoint catalogue; AAP records the judgment that for
*behaviour-modifying writes* specifically, the gate must be
synchronous, pre-write, and terminate at a human rather than a
second LLM — a probabilistic approver fails open under prompt
injection.

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.md) records that after
an incident an operator can reconstruct which inputs reached the
agent, which scaffolding version was active, which approvals gated
that version, which external surface was touched, and what the
final output was. This maps to **§2.1.2 (agent identity)** — an
agent identity should be unique, *accounted for* (tied to an
accountable party), *differentiated according to the capacity in
which it acts* and recorded *for auditability*, with delegations of
authority *clearly recorded* — and to **§2.3.3 (continuous testing
and monitoring)**: log and trace *each step of an agent workflow
and agent-to-agent interactions* to identify points of failure, and
*ensure log immutability* so problematic trajectories cannot be
deleted. The MGF ships identity and logging practices; AAP records
the judgment that reconstruction must be *build-time structural*
(append-only logs, version-pinned scaffolding, approval audit
trails) because runtime observability alone cannot answer the
post-incident question of *which scaffolding version, under whose
approval*.

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.md) records that
every non-weights component of the agent is scaffolding,
materialized as files under version control and changeable only
through the ADR-0005 gate. The MGF has no direct counterpart — the
nearest practices are **§2.3.3 (robust change management)**, which
asks for defined triggers and risk-categorized review for changes
(model updates, tool modifications, autonomy adjustments), and
§1.1.1's decomposition of an agent into named components
(instructions, tools, controls, logging). What the MGF does not
require is that behaviour *live in inspectable, versioned files* at
all; a deployment whose behavioural constraints exist only as
vendor-side configuration would satisfy the MGF's change-management
practices while failing ADR-0007. AAP records this as a judgment
the framework leaves open rather than a conflict: the MGF
catalogues *how to review changes*; ADR-0007 constrains *where the
changed thing is allowed to live*.

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.md) records that each
agent process is bound at deployment time to exactly one
identifiable human accountable operator. This maps to **§2.2.1
(clear allocation of responsibilities)** and to **§2.4.2**, which
requires giving users *the respective human contact points who are
responsible for the agents*. But the mapping carries an explicit
divergence: §2.1.2 permits an agent identity to be tied to *a
supervising agent, a human user, or an organisational department*.
ADR-0008 records the judgment that the chain's endpoint must be one
named human per agent process — a department endpoint reproduces
the shared-accountability configuration in which the
moral-crumple-zone failure lands on whoever is operationally
proximate, and a supervising-agent endpoint defers the question by
one hop without answering it. Status: **experimental** — and the
divergence is exactly the kind of organizational-diversity question
the experimental status is waiting on.

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.md) records that
before adopting an autonomous-loop architecture, the work is
triaged along the four Business AI Quadrants, and that choosing the
Autonomous Agentic Loop Quadrant commits the organization to naming
a gap-bearer at deployment time. This maps to **§2.1.1 (determine
suitable use cases)** — *not all use cases are suitable for agents,
and some may be better served by deterministic workflows*, with
likelihood factors that are precisely AAP's triage inputs (level of
autonomy, task complexity, system complexity) — and to §2.1.2's
closing practice, *evaluating the residual risks*: organizations
should *determine if the residual risk for their agentic deployment
is of a tolerable level and can be accepted*. The convergence stops
one step short of the ADR: the MGF requires that residual risk be
*accepted*, but does not require that an identifiable subject be
named to *bear* it. ADR-0009 records that for the Autonomous
Agentic Loop Quadrant the residual is not a quantity but an
attribution gap, and acceptance without a named bearer is the
moral-crumple-zone configuration. Status: **experimental**.

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.md) records that
operation-phase placement of an Autonomous Agentic Loop Quadrant
component requires an explicit Phase-crossing decision: when a new
pattern surfaces in operation, does the loop handle it in place, or
route it back to design? The MGF's lifecycle spine maps well:
**§2.3.1 / §2.3.2 / §2.3.3** separate design-time controls,
pre-deployment testing, and post-deployment monitoring; §2
frames the four dimensions as *an iterative process* in which an
anomaly detected in monitoring sends the organization back to
re-assess and re-bound — that arrow *is* a route-back-to-design
edge; and §2.3.3's *robust change management* requires defined
triggers for re-entering review. The MGF ships the feedback loop as
a practice; AAP records the judgment that for autonomous loops the
choice between *handle dynamically in place* and *route back to
design* must be made explicitly at deployment time, because "we'll
figure it out when it happens" delegates the Phase-crossing
decision to the autonomous loop itself, which inherits the same
gap. Status: **experimental**.

## Per-Quadrant note

The MGF does not categorize agentic work along AAP's quadrant axes;
it scales practices by risk factors (§2.1.1) instead. The two
gradings compose rather than compete:

- **Quadrant 3 (LLM Workflow)** — the MGF's risk-factor table
  *already prices the quadrant boundary*: low autonomy ("agent is
  provided with a SOP") and low system complexity ("a single agent
  carrying out a sequential workflow") sit on the low-likelihood
  end of §2.1.1. The six applicable ADRs (0001, 0003, 0004, 0005,
  0006, 0007) populate the §2.1–2.3 practices at medium control
  density.
- **Quadrant 4 (Autonomous Agentic Loop)** — the high end of the
  same factors (agent *using its own judgment*, multi-agent
  interaction, emergent behaviour per §1.2.3). All ten ADRs apply;
  ADR-0008 / 0009 / 0010 are load-bearing here, against §2.2
  (human accountability), §2.1.1 (use-case triage and residual
  risk), and §2.3.3 (post-deployment monitoring and change
  management) respectively.

## Convergence and tension — the value chain and the gap-bearer

The MGF's §2.2 answer to accountability diffusion is a *chain*:
responsibilities allocated across the agentic value chain (model
developers, tooling providers, platform providers, system providers
/ app developers, deployer, end users — §2.2.1, refined in v1.5),
across internal teams, and across contracts with external parties.
The framework is candid that the problem it addresses is
*diffusion* — multiple actors involved in different parts of the
lifecycle.

AAP's reading: a chain is the right structure for distributing
*work*, and §2.2.1's contractual practices (clarify distribution of
obligations, address third-party opacity) are real mechanism. But a
chain distributes; it does not terminate. Every link added is also
a potential transfer point, and under a multi-link chain with
ambiguous involvement, post-incident attribution can circulate
indefinitely — each link pointing at the next — unless some link is
designated, in advance, as the place where circulation stops.
That terminal designation is what
[ADR-0008](../adr/0008-one-agent-one-human.md) (one named human per
agent process) and [ADR-0009](../adr/0009-triage-before-autonomy.md)
(a named gap-bearer as the price of autonomy) add to the MGF's
chain. The two layers are compatible — the chain allocates the
distributable part; the named bearer absorbs the part that does not
distribute — but they are not the same claim, and adopting the
chain without the terminal designation leaves the
moral-crumple-zone configuration reachable.

## Reverse index — MGF section → applicable ADR(s)

| MGF for Agentic AI section (and subject) | Applicable AAP ADR(s) |
|---|---|
| **§1.2.3** — systemic and multi-agent risks | 0003, 0004, 0009 |
| **§2.1.1** — determine suitable use cases; risk factors; threat modelling | 0001, 0003, 0009 |
| **§2.1.2** — agent limits (deterministic bounding, least privilege) | 0001, 0002, 0004 |
| **§2.1.2** — agent identity (unique, accounted-for, recorded delegation) | 0006, 0008 |
| **§2.1.2** — evaluating the residual risks | 0009 |
| **§2.2.1** — allocation of responsibilities (value chain, contracts) | 0008 |
| **§2.2.2** — meaningful human oversight (checkpoints, approval audit, fail-closed default) | 0005 |
| **§2.3.1** — design-time technical controls (structural vs prompt-layer) | 0002, 0003 |
| **§2.3.2** — pre-deployment testing (incl. multi-agent-level testing) | 0010 |
| **§2.3.3** — continuous monitoring; log immutability; change management | 0006, 0007, 0010 |
| **§2.4.2 / §2.4.3** — end-user transparency; human contact points | 0008 |

## Caveat

The MGF for Agentic AI is explicitly a living document: v1.5
arrived less than four months after v1.0 and restructured material
this file cites (the value chain, multi-agent risks). Section
numbers and practice wording should be expected to move. This
mapping will be re-checked on each MGF version and at least yearly.
Mapping shifts do not propagate into the ADRs themselves. The ADRs
and this file decay on independent cadences; the ADRs are the
durable anchor.
