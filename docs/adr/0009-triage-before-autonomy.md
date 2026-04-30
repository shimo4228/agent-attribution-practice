# ADR-0009: Triage Before Autonomy (experimental)

> **Summary.** Before adopting an autonomous-loop architecture (one in
> which the agent decides each next step at runtime), triage the work
> along two axes — deterministic vs semantic-judgment, and
> pre-defined-workflow vs exploratory. Reserve the autonomous loop
> for the quadrant that genuinely requires open-ended exploration.
> Choosing the autonomous loop commits the deploying organization to
> absorb a non-removable attribution gap as a named, pre-deployment
> cost — not a post-incident discovery.

## Status
**experimental** — the principle follows structurally from the trilogy
of essays (2026-04-06 / 04-13 / 04-14) and the architectural
follow-ups (2026-04-29 / 04-30), but the operational signals for
distinguishing post-hoc separable architectures from blended ones at
design time are still being refined. See open questions at the end.

## Date
2026-04-30 (first formalization in this repository)

## Context

The trilogy and the eight earlier ADRs answer "what should be
constrained, and who is responsible." Each answer assumes a structural
premise that is rarely stated: each judgment element in the system is
**post-hoc separable** — when something goes wrong, the operator can
trace the failure to a specific component (a model selection, a
routing rule, an upstream data source) and route the responsibility
there. This is what makes [`accountability distribution`](../glossary.md#accountability-distribution)
operate. Distribution requires a subject who can be redirected to.

The ReAct-style autonomous loop violates that premise. In an
autonomous-loop architecture, each iteration's role is decided at
runtime — the model output, tool selection, history reference, and
prompt context blend into a single stream of judgments. When the
output is wrong, the contributions cannot be untangled after the
fact. [`Attribution gap`](../glossary.md#attribution-gap) is the name
for this foreclosed separability. It is intrinsic to autonomy, not a
maturity problem that better tooling will solve.

The 2026-04-29 essay introduced a four-quadrant decomposition of
business AI work along two axes:

| | Pre-defined workflow | Exploratory |
|---|---|---|
| **Deterministic** | Script Quadrant | Algorithmic Search Quadrant |
| **Semantic-judgment** | LLM Workflow Quadrant | Autonomous Agentic Loop Quadrant |

The 2026-04-30 essay observed that the industry's standard vocabulary
has no positive name for the LLM Workflow Quadrant. "Anything that
isn't deterministic" gets routed to the Autonomous Agentic Loop
Quadrant by elimination. The result is two distinct redirect failure
modes that have been confused with each other:

- **Artificial redirect impossibility.** LLM Workflow Quadrant work
  routed through autonomous-loop architecture. Resolvable by
  re-architecting.
- **Principled redirect impossibility.** Autonomous Agentic Loop
  Quadrant work, where blending genuinely forecloses redirect.
  Not resolvable architecturally.

Most current accountability discussion (sandbox strength, HITL
overload, *moral crumple zone* — Elish 2019) addresses the artificial
case. The principled case — what to do when a task genuinely requires
open-ended exploration *and* attribution must still distribute — has
barely entered the discourse.

The earlier ADRs cannot resolve this on their own, because the
architectural choice that creates the gap is upstream of every
constraint they describe. A sandbox bounds blast radius (ADR-0004);
it does not restore redirect-ability. An approval gate gates external
side effects (ADR-0005); it does not separate blended judgments.
The triage decision belongs at design time, before the rest of the
ADRs become applicable.

## Decision (experimental)

Before adopting an autonomous-loop architecture, triage the work
along the two axes of the four quadrants:

1. **Script Quadrant** (deterministic × pre-defined). Use scripts
   and pipelines. No LLM required.
2. **Algorithmic Search Quadrant** (deterministic × exploratory).
   Use classical algorithms — search, dynamic programming, MCTS,
   reinforcement learning. Out of scope for this repository's
   LLM-focused ADRs.
3. **LLM Workflow Quadrant** (semantic-judgment × pre-defined). Use a
   structured-workflow architecture: deterministic control flow
   plus bounded LLM calls with named, documented roles and explicit
   input/output schemas. This is the documented default for most
   current LLM applications.
4. **Autonomous Agentic Loop Quadrant** (semantic-judgment ×
   exploratory). Use the autonomous loop *only when the work itself
   requires open-ended exploration* — the path is not predictable in
   advance, and bounding the LLM call's role would prevent the work
   from being done at all.

Choosing the Autonomous Agentic Loop Quadrant commits the deploying
organization to absorb the attribution gap as a **named,
pre-deployment cost**. The named cost has two components:

- **An organizationally identifiable subject** who carries the gap.
  In current practice, this is a human (consistent with ADR-0008
  [One Agent, One Human](0008-one-agent-one-human.md)). It cannot be
  the agent itself; agents are not yet legal subjects. It cannot be
  resolved post-incident; the *moral crumple zone* literature shows
  the burden then falls on the operator with the least actual
  control.
- **An explicit acknowledgement** in the deployment record that
  redirect for this work will not succeed at the level of separable
  contributions. Failure analysis will identify *that* the system
  failed; it will not produce a clean redirect target.

The triage decision is made at design time, by humans, and recorded
alongside the architectural choice.

### What this permits

- **Hybrid architectures** that combine quadrants are compatible if
  the boundary between them is itself a structural choice and the
  Autonomous Agentic Loop portion is treated as such (with a named
  gap-bearer). Example: clause extraction in the LLM Workflow
  Quadrant, escalating to an approval-gated Autonomous Agentic Loop
  for novel-clause analysis.
- **Moving work between quadrants** as the work matures. Tasks that
  start as exploratory often become structured once the path is
  understood; re-architecting from Autonomous Agentic Loop to LLM
  Workflow is a legitimate maturation, not a regression.
- **Routing decisions made by LLMs** within the LLM Workflow Quadrant
  (Anthropic's *routing* pattern). Routing is a single bounded
  judgment with a documented schema; it does not blend judgments at
  runtime.

### What this forbids

- **Defaulting to the Autonomous Agentic Loop** for work that is
  semantic but pre-defined. The category error this repository names.
- **Deferring the triage to runtime.** The decision to enter
  autonomy is itself an architectural commitment; letting the agent
  decide when to escalate inherits the same gap that motivated the
  triage.
- **Naming the gap-bearer post-incident.** If no organizationally
  identifiable subject was named at deployment time, the work is in
  the Autonomous Agentic Loop Quadrant *without a gap-bearer* — the
  configuration this ADR exists to prevent.
- **Treating sandbox strength as a substitute for triage.** Sandboxing
  bounds blast radius (ADR-0004 territory). It does not restore
  separable contribution.

## Alternatives Considered

- **Adopt autonomous-loop architecture by default; constrain via
  guardrails.** Rejected. Guardrails are signposts in the sense of
  ADR-0001's critique. The architectural choice itself foreclosed
  redirect; no guardrail restores it.
- **Sandbox the autonomy strongly enough to make it safe.** Rejected
  as a substitute. Sandboxing addresses blast radius (ADR-0004), not
  redirect-ability. A perfectly sandboxed autonomous loop still has
  an attribution gap.
- **Defer the triage to runtime; let the agent decide when to
  escalate out of the loop.** Rejected. Runtime triage is an
  autonomous decision and inherits the same gap. The decision
  belongs at design time.
- **Treat the four quadrants as agent categories rather than work
  categories.** Rejected. The quadrants describe the *work*, not the
  agent. The same agent process can be deployed against different
  quadrants of work; what changes is the architecture chosen for
  that work.
- **Skip the triage, rely on ADR-0005 (approval gate) and ADR-0008
  (one operator) to catch the consequences.** Rejected. Both ADRs
  become more load-bearing under autonomy, not less. They cannot
  recover separability that the architectural choice has already
  destroyed.

## Consequences

- The LLM Workflow Quadrant becomes the documented default for most
  current LLM applications. The vocabulary normalizes that default
  rather than treating it as the deviation from "real" agents.
- When the Autonomous Agentic Loop *is* chosen, the cost is paid
  upfront. ADR-0005 (approval gate), ADR-0006 (causal traceability),
  and ADR-0008 (one operator) become more load-bearing under
  autonomy, not less.
- Designers must distinguish post-hoc separable architectures from
  blended ones at design time. Practical signals: each LLM call has
  a documented role; each call has an input/output schema; failures
  map to a specific component. If those signals do not hold, the
  architecture is in the Autonomous Agentic Loop Quadrant whether
  or not it was named that way.
- The four-quadrant decomposition becomes a permanent diagnostic
  frame paired with the nine ADRs — together forming a two-axis
  structure: the ADRs answer per-question, the quadrants route the
  work to where those answers apply.
- Vendors and reference architectures that publish autonomous-loop
  templates without naming the attribution-gap commitment are doing
  what ADR-0001 calls *signposts on a climbable wall* — the pattern
  this repository is designed against.

## Open questions (why this is experimental)

1. **Boundary cases.** Multi-turn chat agents with bounded
   system-prompt-defined roles — separable enough to count as LLM
   Workflow Quadrant, or already in Autonomous Agentic Loop
   territory? The answer depends on whether the role bounds hold
   under prompt injection and unusual user input; criteria are not
   yet sharp.
2. **Operationally measurable proxies for post-hoc separability.**
   "Each call's contribution is separable" is currently a design
   judgment. Empirical signals (call-graph stability across runs,
   consistency of role under perturbation, schema compliance rate)
   may make the judgment more transferable, but the right metric set
   is open.
3. **Interaction with ADR-0008 when no responsibility subject
   exists.** If the work genuinely requires the Autonomous Agentic
   Loop Quadrant *and* no organizationally identifiable subject can
   bear the gap (high-stakes decisions outside any individual's
   authority — large-scale legal, medical, fiscal action), is the
   right decision to not deploy, to deploy with a contractual party
   (insurance pool, regulatory body) as the bearer, or to wait for
   institutional infrastructure that does not yet exist? This is
   manifesto territory.
4. **Interaction with regulatory regimes.** Some domains will
   regulate the Autonomous Agentic Loop Quadrant directly (the EU AI
   Act's high-risk classification is a near-precedent). Whether the
   triage decision should reference such regimes by name, or remain
   architecturally framed and let regulation map onto the quadrants
   externally, is unresolved.

This ADR will be revised as operational experience accumulates with
the Quadrants vocabulary in actual deployments. The status will move
from experimental to accepted once the boundary cases have documented
answers, or to rejected / superseded if the triage frame turns out to
be the wrong frame.

## Consequences for the other ADRs

ADR-0009 sits before ADRs 0001–0008 in the order of application:

- **Script Quadrant** — ADRs 0001–0008 are largely out of scope; the
  work does not involve an LLM-equipped agent.
- **Algorithmic Search Quadrant** — out of scope for this repository.
- **LLM Workflow Quadrant** — ADRs 0001–0007 apply. ADR-0008 applies
  if the workflow has an external adapter that sends side effects;
  the operator binding is meaningful but lighter weight.
- **Autonomous Agentic Loop Quadrant** — *all nine* ADRs apply, and
  ADR-0005, ADR-0006, ADR-0008 carry the most weight because they
  are what makes the named gap-bearer's commitment operational.

This is why ADR-0009 belongs in the same document set as ADRs
0001–0008: not as a successor, but as a triage that determines which
of the eight become load-bearing for a given piece of work.

---

**Lineage.** Article 4 ("[Where ReAct Agents Are Actually Needed in
Business](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do)",
2026-04-29) introduced the four-quadrant framing of business AI work.
Article 5 ("[The LLM Workflow Quadrant Is Missing from Our
Vocabulary](https://dev.to/shimo4228/3-the-llm-workflow-quadrant-is-missing-from-our-vocabulary-n18)",
2026-04-30) named the principled vs artificial redirect impossibility,
the moral crumple zone (Elish 2019), and the absent accountability
subject. The earlier essays' source naming ("ReAct Quadrant",
"Classical AI Quadrant") is preserved as alias in [`../glossary.md`](../glossary.md);
this ADR uses the renamed forms to keep the Decision body
harness-neutral. Parallel to ADR-0008 ([One Agent, One
Human](0008-one-agent-one-human.md)): 0008 binds the accountable
human; 0009 binds the architectural choice that makes 0008's binding
meaningful.
