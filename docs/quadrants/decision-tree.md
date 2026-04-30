# Decision Tree — Routing a Piece of Work to a Quadrant

> **Purpose.** Convert ADR-0009 [Triage Before
> Autonomy](../adr/0009-triage-before-autonomy.md) and ADR-0010
> [Phase Separation](../adr/0010-phase-separation.md) into a
> five-question navigator usable in design reviews. Each branch
> references the ADRs that become load-bearing once the quadrant
> and phase are chosen, so this is a *judgment chain navigator*,
> not a fixed framework.

## The five questions

Q1–Q4 are the [problem-space triage from
ADR-0009](../adr/0009-triage-before-autonomy.md). Q0 surfaces the
*Phase-crossing decision* from
[ADR-0010](../adr/0010-phase-separation.md) when the work is
operation-phase and the result of Q1–Q4 is the Autonomous Agentic
Loop Quadrant.

Phase and Quadrant are **independent dimensions** — every Quadrant
can appear in either phase. Q0 is not a Quadrant filter; it is a
prompt to surface the Phase-crossing decision when it is
load-bearing.

### Q0. Is this work in the design phase or the operation phase?

- **Design phase** (exploratory work, workflow-being-built,
  prototyping, unknown structure being analyzed) → continue to
  Q1–Q4. All Quadrants are legitimate candidates depending on the
  work. Coding agents and Deep Research tools are typically here
  as design-phase residents (each session is an independent design
  session).
- **Operation phase** (deployed workflow running on its known
  path, predictability is the requirement) → continue to Q1–Q4.
  All Quadrants are still candidates depending on the work, but
  *if* Q1–Q4 yields the Autonomous Agentic Loop Quadrant, the
  Phase-crossing decision must be recorded explicitly (see Q4
  Part B below). The empirical default for operation-phase
  workflows is Quadrant 1 + 3 (+ 2 where applicable), but this
  is a default, not a requirement.

The Phase decision is itself recorded alongside the deployment.
"This is operation phase, default composition (Quadrant 1 + 3)" is
a complete record. "This is operation phase, Quadrant 4 admitted,
Phase-crossing decision: route new patterns back to design" is a
complete record. "We didn't decide" is not.

## The four problem-space questions

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Can the work be expressed as deterministic rules?        │
│    (no semantic judgment required at any step)              │
└─────────────────────────────────────────────────────────────┘
            │
        YES │                                    NO
            │                                     │
            ▼                                     ▼
┌─────────────────────────────────┐   ┌─────────────────────────────────┐
│ 2. Is the search space          │   │ 3. Can the workflow             │
│    static and finite?           │   │    (the role of each LLM call)  │
│                                 │   │    be defined in advance?       │
└─────────────────────────────────┘   └─────────────────────────────────┘
        │             │                      │             │
    YES │         NO  │                  YES │         NO  │
        │             │                      │             │
        ▼             ▼                      ▼             ▼
   ┌────────┐    ┌────────────┐         ┌─────────┐    ┌─────────────────┐
   │ Script │    │ Algorithmic│         │   LLM   │    │ 4. Can a        │
   │Quadrant│    │   Search   │         │ Workflow│    │    pre-named    │
   │        │    │  Quadrant  │         │ Quadrant│    │   gap-bearer    │
   │        │    │            │         │         │    │  be identified? │
   └────────┘    └────────────┘         └─────────┘    └─────────────────┘
                                                              │
                                                          YES │      NO
                                                              ▼      │
                                                       ┌────────────┐│
                                                       │ Autonomous ││
                                                       │  Agentic   ││
                                                       │Loop Quadrant││
                                                       └────────────┘│
                                                                     ▼
                                                          ┌────────────────┐
                                                          │ Decision: do   │
                                                          │ not deploy     │
                                                          │ (gap unmanaged)│
                                                          └────────────────┘
```

## Question-by-question detail

### Q1. Can the work be expressed as deterministic rules?

If the work can be specified completely with rules — "given this input
shape, produce this output by these steps, no semantic judgment
required" — the answer is yes. Form filling, data validation,
threshold checks, schema transformations, lookup-and-format work.

- **Yes →** continue to Q2.
- **No →** continue to Q3 (an LLM is in the loop somewhere).

The test for "deterministic": can two reasonable engineers produce
identical outputs without consulting each other, given the same input
and the same specification? If yes, no semantic judgment is required.

### Q2. Is the search space static and finite?

For deterministic work, distinguish *enumerable* tasks from
*exploratory* tasks.

- **Yes →** [`Script Quadrant`](README.md#1-script-quadrant). Use
  scripts, pipelines, workflow engines. The ten ADRs are largely out
  of scope for this work.
- **No →** [`Algorithmic Search Quadrant`](README.md#2-algorithmic-search-quadrant).
  Use classical search algorithms — A*, dynamic programming, MCTS,
  reinforcement learning, integer programming, constraint solvers.
  Out of scope for this repository's LLM-focused ADRs; standard SE
  accountability applies.

### Q3. Can the workflow be defined in advance?

For LLM-involved work, this is the central triage question. The
"workflow" here means: *the role of each LLM call, the order in which
calls are made, and the data flow between them.*

- **Yes →** [`LLM Workflow Quadrant`](README.md#3-llm-workflow-quadrant).
  Use deterministic control flow plus bounded LLM calls with
  named, documented roles and explicit input/output schemas. This is
  the default for most current LLM applications. **Apply ADRs:**
  - [ADR-0001](../adr/0001-security-by-absence.md) — capabilities not
    in the codebase cannot be invoked
  - [ADR-0003](../adr/0003-untrusted-content-boundary.md) —
    accumulated state cannot grant authority
  - [ADR-0004](../adr/0004-single-external-adapter.md) — blast
    radius bounded by design
  - [ADR-0005](../adr/0005-human-approval-gate.md) — behavior
    changes need human sign-off
  - [ADR-0006](../adr/0006-causal-traceability.md) — every event
    reconstructible
  - [ADR-0007](../adr/0007-scaffolding-visibility.md) — behavior
    lives in files
- **No →** continue to Q4.

The test for "workflow definable in advance": can you draw the
sequence of LLM calls on a whiteboard *before* running the system,
labeling each call's role and schema? If yes, the workflow is
pre-defined. If the next call's role depends on what the previous
call's output happened to be, the workflow is exploratory.

### Q4. Can a pre-named gap-bearer be identified, and (if Q0=operation) is the Phase-crossing decision recorded?

Reaching this question means the work genuinely requires the
**Autonomous Agentic Loop Quadrant** — the path is not predictable in
advance, and bounding the LLM call's role would prevent the work from
being done. The architecture will blend judgment elements at runtime;
post-hoc separability is foreclosed; redirect cannot succeed at the
level of separable contributions. The
[`attribution gap`](../glossary.md#attribution-gap) is intrinsic.

The question has two parts; both must be answered yes for deployment.

**Part A (always required).** Who absorbs the attribution gap?
ADR-0009 forbids leaving this to post-incident discovery (the
*moral crumple zone* failure mode).

**Part B (only if Q0 = operation phase).** Is the Phase-crossing
decision recorded — i.e., when a new pattern surfaces in operation,
will it be handled by the autonomous loop in place, or routed back
to the design phase as feedback? ADR-0010 forbids implicit
Phase-crossing decisions for operation-phase Quadrant-4 placements.
For design phase placements, this part is automatically satisfied
(the design phase *is* where Phase-crossing happens).

- **Yes (both parts) →** [`Autonomous Agentic Loop Quadrant`](README.md#4-autonomous-agentic-loop-quadrant).
  Apply **all ten ADRs**; ADRs 0005, 0006, 0008, 0009, 0010 carry
  the most weight because they are what makes the named gap-bearer's
  commitment operational and the lifecycle placement explicit. The
  gap-bearer (and Phase-crossing decision, when applicable) are
  recorded alongside the architectural choice at deployment time.
- **No →** **Decision: do not deploy.** The configuration the ADR
  set is designed to prevent — Autonomous Agentic Loop Quadrant
  work running either without a gap-bearer (Part A failure) or
  without an explicit Phase-crossing decision in operation (Part B
  failure).

The test for "gap-bearer identifiable": can the deployment record
name a specific human (per ADR-0008), an organizationally
identifiable role with formal succession, or a contractual party
(insurance pool, regulatory body) who acknowledges, at deployment
time, that they bear responsibility for failures of this work? If
none of these can be named, the answer is no.

The test for "Phase-crossing decision recorded": can the deployment
record state, in one sentence, what happens to a new pattern that
surfaces during operation? "Route to design as feedback" and "Handle
in the autonomous loop in place" are both admissible answers.
"We'll figure that out when it happens" is not.

## Hybrid architectures

Real systems often combine quadrants. ADR-0009 permits hybrids when
the boundary between quadrants is itself a structural choice and the
Autonomous Agentic Loop portion is treated as such (with a named
gap-bearer for that portion).

Common shapes:

- **Plan-and-Execute** — autonomous loop generates the plan; LLM
  Workflow executes each step. The autonomous portion is the planning
  phase.
- **Router agent** — LLM Workflow with a routing call that classifies
  inputs into branches. The router itself is bounded; the branches
  are bounded.
- **Tiered handoff** — LLM Workflow handles common cases; escalates
  to (approval-gated) Autonomous Agentic Loop for novel cases.

For each hybrid, run the decision tree on each portion separately.
The autonomous portion still needs Q4 answered.

## When to re-run the triage

The triage is a design-time decision, but the answer can change as
the work matures:

- **Phase transitions.** Work that begins in the design phase often
  matures into operation as the path becomes understood. Re-run Q0
  when this happens; the resulting Quadrant placement may shift
  from 4 to 3 (a legitimate maturation, not a regression).
- **Quadrant transitions.** Tasks that start in the Autonomous
  Agentic Loop Quadrant often move to the LLM Workflow Quadrant
  once the path is understood. Re-run Q1–Q4.
- **Vendor capability shifts.** A capability that was formerly out
  of reach (Q1=No, Q3=No) may become workflow-able as models and
  tooling improve, moving the work from Quadrant 4 to Quadrant 3.
- **Phase-crossing decision shifts.** An operation-phase deployment
  that had recorded "route new patterns back to design" may, after
  experience, decide to switch to "handle in the autonomous loop in
  place" — or vice versa. Re-record the Phase-crossing decision
  alongside the architectural change.
- **Regulatory shifts.** A regulatory change can change the answer
  to Q4 (whether a gap-bearer can be named for a given domain).

When in doubt, re-run the five questions and document the new
answers alongside the architectural change.
