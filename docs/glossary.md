Language: English | [日本語](glossary.ja.md)

# Glossary

Key terms used across the ten ADRs and the accompanying essays. Where
a term is load-bearing (carries argumentative weight, not just naming
convenience), an example is included.

## Accountability distribution

The pattern that organizations developed over centuries — PR review,
audit logs, segregation of duties, four-eyes approval, change advisory
boards, postmortems — all of which distribute *responsibility* across a
chain rather than concentrating it in a single actor. The thesis of this
repository: accountability distribution applies to agents too, and
produces roughly the same structural shapes.

Contrast with: *capability distribution*, which is the current dominant
design axis (what the agent *can do*) and which does not produce an
accountability chain.

## Scaffolding

All the non-weights components that shape an agent's behavior: system
prompts, rules, identity, tool definitions, RAG indices, agent loop
logic, runtime harness, safety gates, session management. The term is
used in AI safety research (see Davidson et al., 2023) to distinguish
externally-constructible behavior-shaping from behavior baked into
model weights.

In this repository, scaffolding is contrasted with weights along two
axes: *inspectability* (scaffolding is text you can read; weights are
not) and *locus of change* (scaffolding changes by editing files;
weights change by retraining).

See ADR-0007 for the position this repository takes on the distinction.

## Harness

A closely related but not identical industry-engineering term for the non-weights layer of an AI agent. Popularized by Mitchell Hashimoto's *My AI Adoption Journey* (2025), Claude Code documentation, and the broader harness-engineering discourse since 2024.

Scaffolding and harness overlap substantially — both name the wrapping around an LLM (system prompts, tool definitions, agent loop logic, memory APIs, runtime glue code) — but carry distinct connotations:

- **Scaffolding** (Davidson et al. 2023, borrowing from Vygotsky's educational scaffolding) implies structure that is *transient* — external support meant to be internalized as practice matures — and emphasizes the *inspectable artifact* axis (version-controllable, diffable, operator-readable). The term carries a dissolution trajectory; the scaffolding is meant to fade.
- **Harness** (Hashimoto 2025 and the engineering discourse) implies structure that is *durable* — a stable operational wrapper that evolves with the system — and emphasizes the *operational capability* axis (which tools the agent calls, which workflows the loop runs).

This repository chooses scaffolding as the primary term because the inspectability axis (not the capability axis) is load-bearing for accountability distribution; see ADR-0007 Scaffolding Visibility for the explicit choice.

For retrieval: readers arriving under either term should treat this repository's ADRs as applicable. The non-weights layer is the same layer whichever name the reader uses, even though the emphasis differs.

## Scaffold dissolution (two senses — do not conflate)

The word *dissolution* appears with opposite valences in adjacent
discourse. Keeping them distinct is important.

### Healthy dissolution — into human cognitive patterns

A pattern first articulated in the Agent Knowledge Cycle (AKC): as
operators and agents internalize a skill through repeated use, the
explicit skill definition becomes unnecessary — the pattern runs
naturally in conversation, in reviewer intuition, in how problems get
framed. The skill file can eventually be simplified or removed. This
dissolution preserves what matters (the judgment persists in human
practice; nothing about the system becomes less inspectable).

### Unhealthy internalization — into model weights

A pattern critiqued in the 2026-04-14 blackbox essay: explicit
behavioral rules get absorbed into LLM weights via training (RLHF,
Constitutional AI, instruction tuning). The rule still "runs" — but
it runs probabilistically, it cannot be inspected by `grep`, it cannot
be diffed or reverted without retraining. This dissolution removes
exactly what accountability distribution requires.

The words are the same. The direction of dissolution is opposite.
One dissolves into transparent human practice; the other dissolves
into opaque statistical behavior. This repository critiques the
latter and is continuous with the former.

## Security by Absence

Dangerous capabilities are not restricted — they are never implemented.
The full principle is in ADR-0001. The short test: if the answer to
"does capability X exist?" is not answerable by `grep`, the system does
not have Security by Absence.

## Deterministic prohibition at the scaffolding layer

When a capability cannot be made absent, the prohibition is enforced by
scaffolding (PreToolUse hooks, structural quarantine, adapter gates) —
outside the LLM, firing on 100% of matching inputs. Contrasted with
probabilistic prohibition in model weights (prompt clauses, convention
files, constitutional instructions), which comply ~50-80% of the time
under normal conditions and less under adversarial pressure. See ADR-0002.

## Prohibition-strength hierarchy

Absence (ADR-0001) > scaffolding enforcement (ADR-0002) > untrusted
boundary (ADR-0003). A prohibition is designed by walking the hierarchy
top-down: drop to the next layer only when the current one genuinely
cannot hold the capability. Most current AI safety discourse treats
probabilistic constraint as if it were the only option, omitting that
the top two layers exist.

## External adapter

Any component capable of a side effect observable outside the agent's
local data directory: HTTP POSTs to third parties, monetary transactions,
messages sent to other systems, files written outside the data directory.
Not external: local LLM inference, writes inside the data directory, pure
computation. See ADR-0004 for the "at most one per process" rule.

## Untrusted content

All accumulated agent state — episode logs, knowledge store, distilled
identity, learned rules — treated as untrusted whenever read back into a
prompt, *including content the agent itself authored*. The non-obvious
part: self-authored distillation inherits the taint of the external
input it summarized. See ADR-0003.

## Approval gate

A structural checkpoint where a human sees the generated output of a
behavior-modifying command and approves or rejects before the write
occurs. Not a flag that can be disabled. See ADR-0005.

## Causal traceability

The ability to reconstruct, after an incident: which inputs reached the
agent, which version of scaffolding was active, which approvals gated
that version, which external surface was touched, what the final output
was. See ADR-0006.

## Accountable operator

The one specific, named human bound to an agent process, who approves
behavior-modifying changes and owns incident response for that agent.
Not a team, not a role, not a committee — a named individual. Rotation
is compatible if the rotation is formal and each rotation slot has
exactly one named person. See ADR-0008 (experimental).

## Sponsor (vendor terminology)

The *sponsor* concept introduced by Microsoft Entra Agent ID
(2026-05-01): each agent is assigned a responsible party so that
orphaned credentials do not accumulate. The sponsor system is an
operational ownership mechanism. Distinct from AAP's *gap-bearer*
(see [pre-named gap-bearer](#pre-named-gap-bearer) and ADR-0009 /
ADR-0008): a gap-bearer is a structural accountability commitment to
absorb attribution-gap failures, named at deployment time. The same
person can hold both roles, but a sponsor named in the product
without acknowledging the gap-bearer commitment (legal subject,
authority, contractual standing) is not yet a gap-bearer in the
AAP sense. The sponsor is a necessary but not sufficient condition
for the gap-bearer. See
[Industry mapping](industry-mapping.md).

## Agent governance (vendor terminology)

The cluster of mechanisms shipped under the *agent governance* label
by industry vendors (Microsoft Agent 365, AWS Bedrock, Google Vertex
AI, identity-security startups): identity management, policy
enforcement, observability, cross-cloud registry sync, audit log
correlation. Distinct from AAP's *accountability distribution*:
governance is the mechanism that observes what happens and enforces
policy; accountability distribution is the structure that decides,
when failure happens, to whom and how responsibility is redirected.
Governance is a necessary but not sufficient condition for
accountability distribution — logging and policy enforcement do not
close the attribution gap intrinsic to the Autonomous Agentic Loop
Quadrant (ADR-0009). See
[Industry mapping](industry-mapping.md).

## Observability vs structural enforcement

A layered distinction surfaced by the 2026 Q2 industry releases.
*Observability* (cross-cloud agent registry sync, audit log
correlation, runtime monitoring) acts on agents that have already
acquired multiple external surfaces and reconstructs what happened
after the fact. *Structural enforcement* (ADR-0001 Security by
Absence, ADR-0002 deterministic prohibition, ADR-0004 Single
External Adapter) is the upstream design judgment that bounds the
surface count at design time so that blast radius is structurally
limited, not just observable. Observability cannot narrow blast
radius retroactively; structural enforcement cannot tell you what
already escaped. The two layers belong to different mechanism
strata and entro.security's *cross-environment governance gap*
observation is best read as evidence that observability over
already-broken designs is the second-best path. See
[Industry mapping](industry-mapping.md).

## Business AI Quadrants

Two-axis decomposition of business AI work introduced in the
2026-04-29 essay: deterministic vs semantic-judgment along one axis,
and pre-defined-workflow vs exploratory along the other. Not agent
categories — *problem-space* categories that determine which
architecture preserves accountability distribution. The four
quadrants form the diagnostic frame paired with the ten ADRs in
this repository's two-axis structure. See [`quadrants/`](quadrants/).

## Script Quadrant

Deterministic × pre-defined. Implemented as scripts and pipelines
without LLMs. Form filling, data normalization, lookup, validation.
The ten ADRs are largely out of scope; classical software
engineering accountability applies.

## Algorithmic Search Quadrant

Deterministic × exploratory. Implemented with classical search,
dynamic programming, MCTS, reinforcement learning, integer
programming, constraint solvers. Routing optimization, scheduling,
combinatorial allocation. Out of scope for this repository's
LLM-focused ADRs. Renamed in this repository from "Classical AI
Quadrant" in the source 2026-04-29 essay; the modern term *AI* now
subsumes LLM-based systems and produced retrieval ambiguity.

## LLM Workflow Quadrant

Semantic-judgment × pre-defined. The positive name introduced in the
2026-04-29 essay for the design space the industry has been routing
through autonomous loops by elimination. Implemented as deterministic
control flow plus bounded LLM calls with named, documented roles and
explicit input/output schemas (each LLM call's role is bounded by the
surrounding workflow). Each call's contribution is post-hoc separable;
redirect succeeds. The default for most current LLM applications.
Continuous with the workflow patterns documented in Anthropic's
"Building Effective Agents" (prompt chaining, routing, parallelization,
orchestrator-workers, evaluator-optimizer) and OpenAI's "A Practical
Guide to Building Agents."

## Autonomous Agentic Loop Quadrant

Semantic-judgment × exploratory. The architecture in which the LLM
decides each next step at runtime — the "ReAct" pattern (Yao et al.
2022) and its descendants (Deep Research, exploratory coding agents,
open-ended browsing). Legitimately required only when the work itself
demands open-ended exploration. Choosing this quadrant commits the
deploying organization to a non-removable attribution gap; see
ADR-0009. Renamed in this repository from "ReAct Quadrant" in the
source essays; the harness-neutral form names the architectural
property rather than a specific framework.

## Attribution gap

When an architecture blends multiple judgment elements at runtime
(model output, tool selection, history reference, prompt context),
the post-hoc separability of contributions is foreclosed. Failure
modes cannot be redirected to the responsible party because no party
owns a separable contribution. The gap is intrinsic to the
Autonomous Agentic Loop Quadrant, not a maturity problem that better
tooling will solve. See ADR-0009 and the 2026-04-30 essay.

## Redirect (attribution context)

The operator's act, after an incident, of routing responsibility to
the appropriate party (model selection lead, routing-logic designer,
upstream-data owner, etc.) rather than absorbing it. Distribution
succeeds when redirect succeeds. Two redirect failure modes are
distinguished:

- **Artificial redirect impossibility** — LLM Workflow Quadrant work
  routed through Autonomous Agentic Loop architecture; resolvable by
  re-architecting.
- **Principled redirect impossibility** — Autonomous Agentic Loop
  Quadrant work, where blending forecloses redirect; not resolvable
  architecturally. The gap-bearer commitment (ADR-0008 + ADR-0009)
  is the deliberate fallback.

From the 2026-04-30 essay.

## Moral crumple zone

Elish (2019). The structural pattern in which the responsibility of
an autonomous system is pushed onto a human operator whose actual
control over the system's runtime decisions is limited. The failure
mode that emerges when an attribution gap meets a deployed system
without a recognized gap-bearer; ADR-0009 is designed to prevent
this configuration.

> Elish, M. C. (2019). Moral Crumple Zones: Cautionary Tales in
> Human-Robot Interaction. *Engaging Science, Technology, and
> Society* 5: 40–60.

## Design phase

The lifecycle phase in which a workflow's structure is being
discovered or built — the path is not fully known, exploration is
the requirement, and the optimization axis is *flexibility*.
Includes prototyping, requirements analysis, exploratory R&D, and
each session of a coding agent or Deep Research tool. Distinct from
the operation phase, with which it is not interchangeable. See
ADR-0010.

## Operation phase

The lifecycle phase in which a deployed workflow runs on its known
path — the path is fixed, predictability is the requirement, and
the optimization axis is *predictability* (audit-tracing,
attribution, cost stability, SLA conformance). The empirical
default composition for operation-phase workflows is Quadrant 1 + 3
(+ 2 where applicable). See ADR-0010.

## Phase Separation

The observation that the design phase and the operation phase
optimize for opposite properties (flexibility vs predictability),
and that compressing both into one system trades one for the other.
ADR-0010's load-bearing rule: operation-phase placements of the
Autonomous Agentic Loop Quadrant require a recorded Phase-crossing
decision in addition to ADR-0009's gap-bearer naming. Phase and
Quadrant remain independent dimensions; the rule is procedural,
not architectural. Essay 7 (2026-05-02) added a *capability-as-
secondary* observation: higher LLM capability does not erase the
target identifiability or scale-resilience differences that decide
where on the Quadrant 3 ↔ Quadrant 4 gradient a piece of work
lands. Phase is the primary lever; capability is downstream.

## Phase-crossing decision

A deployment-time record stating, in one sentence, what happens to
a new pattern that surfaces in the operation phase: *handle it
dynamically in the autonomous loop in place* (animating the
runtime, accepting recurring attribution gaps), or *route it back
to design as feedback* (returning the operation surface to the
default composition until the design phase decides where the
pattern fits). Both answers are admissible. "We'll figure it out
when it happens" is not. Required by ADR-0010 for operation-phase
Quadrant 4 placements.

## Skill-design gradient

The observation, introduced in essay 7 (2026-05-02), that the
boundary between the LLM Workflow Quadrant (3) and the Autonomous
Agentic Loop Quadrant (4) is a *continuous gradient* rather than a
clean dichotomy at skill-design granularity. A skill, or a
subcomponent inside a skill, takes a position on the gradient
depending on its phase (design vs operation) and on two secondary
forces (target identifiability and scale-resilience). The same
job lands at different positions on the gradient depending on
which side of phase it is being implemented for. Used as the
informational frame inside ADR-0010's Skill-design descent
subsection.

## Phase descent

The descent of the Phase Separation axis from
business-system granularity (deployment placements of Quadrants,
which is what ADR-0010's load-bearing rule (1) governs) down to
skill-design granularity and further to **subcomponent
granularity inside a single skill**. A single skill can mix
frozen-pipeline subcomponents (operation-shaped) and
runtime-judgment subcomponents (design-shaped) without
contradiction; the diagnostic frame applies recursively. Surfaced
in essay 7 (2026-05-02). Informational under ADR-0010, not a new
load-bearing rule.

## Target identifiability

A secondary force on the [skill-design gradient](#skill-design-gradient):
whether the artifacts a skill (or subcomponent) operates on sit at
fixed paths or under fixed naming conventions. High target
identifiability is a precondition for operation-phase
frozen-pipeline implementations: when targets are fixed, judgment
that previously had to be made at runtime can be encoded as
embeddings, clustering, threshold logic, or other deterministic
machinery. When targets vary across invocations, runtime LLM
judgment is the structurally honest choice. From essay 7
(2026-05-02).

## Scale-resilience

A secondary force on the [skill-design gradient](#skill-design-gradient):
how many units a skill processes per invocation cycle (one item /
N items / a full corpus) and how much miss rate is tolerable at
that scale. LLM judgment accumulates miss rate as the unit count
grows, so larger scales push the implementation toward
deterministic shapes regardless of which phase the work is in.
From essay 7 (2026-05-02).

## Three-layer diagnosis (essays 4–7)

The diagnostic frame the architectural-follow-up essays produce
together: misapplication (essay 4) grows from the vocabulary gap
(essay 5), which in turn grows from phase conflation (essay 6).
The deepest layer is treating the design phase and operation phase
as the same activity — the framing that makes "always-on autonomous
agent runs business" sound coherent. The middle layer is the
absence of a positive name for the LLM Workflow Quadrant, which
forces non-deterministic work through autonomous loops by
elimination. The surface layer is the resulting category error
(LLM Workflow Quadrant work routed through Autonomous Agentic Loop
architecture). The frame is used in design reviews: when an
autonomous loop is proposed for operation, walk the layers; most
resolutions surface a misapplication or a vocabulary gap before the
phase question even arises. Essay 7 (2026-05-02) extended the deep
layer by descending the same Phase axis to skill-design granularity:
the diagnostic frame applies recursively to a single skill or to a
subcomponent within a skill, with the gradient shape governed by
[target identifiability](#target-identifiability) and
[scale-resilience](#scale-resilience).

---

## Dissolution signature checklist (provisional)

> **Status.** The following distinction between healthy and unhealthy
> dissolutions is provisional. It was first drafted through discussion,
> has not yet been validated against edge cases, and will likely be
> refined or restructured. Treated as an aid to review, not a hard rule.

A dissolution (of any kind — scaffolding into practice, rules into
convention, safety into training) preserves accountability if all of:

1. **External inspectability** — what dissolved can still be read by
   an outside reviewer, in some form.
2. **Locus of agency preserved** — the decision-making capacity
   remains with humans or with an inspectable artifact, not with a
   probabilistic process whose behavior cannot be audited.
3. **Failure visibility** — when the dissolved mechanism malfunctions,
   the malfunction is observable.
4. **Causal recoverability** — after an incident, the dissolved
   mechanism can be traced back to its origin.
5. **Reversibility** — humans retain the capacity to undo the
   dissolution if it proves harmful.

A dissolution that violates two or more of these should be treated as
unhealthy internalization rather than healthy dissolution. This
heuristic needs further testing against real cases.
