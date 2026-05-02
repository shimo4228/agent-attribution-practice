Language: English | [日本語](anti-patterns.ja.md)

# Anti-patterns — How the Quadrant Choice Goes Wrong

> **Purpose.** Document the recurring ways business AI deployments
> route work to the wrong quadrant and how to recover. Each
> anti-pattern names the failure mode, why it happens, what breaks,
> and how the [`decision-tree.md`](decision-tree.md) recovers it.

## Operation-phase autonomous loop without a Phase-crossing decision

> "We deployed an autonomous research / triage / analysis agent into
> the production workflow. New patterns? It handles them
> dynamically."

- **Failure mode.** Autonomous Agentic Loop Quadrant component
  placed in operation phase as the default route, without an
  explicit Phase-crossing decision recorded at deployment time.
  The configuration ADR-0010 surfaces (not bans) — it must be
  consciously chosen, not slid into.
- **Why it happens.** "Autonomous agents handle business workflows"
  is the dominant vendor framing. Teams accept it as the default
  framing without naming the cost. New patterns appearing in
  operation are absorbed silently by the loop, not surfaced as
  design-phase feedback.
- **What breaks.** The
  [`attribution gap`](../glossary.md#attribution-gap) fires inside
  production runtime. Each new pattern absorbed dynamically blends
  the attribution further; redirect at the component level is
  foreclosed *and* the deployment record does not say why this
  configuration was chosen. Post-incident review finds an
  autonomous loop running operation-phase work with the gap-bearer
  named (per ADR-0009) but no Phase-crossing decision recorded —
  the [*moral crumple zone*](../glossary.md#moral-crumple-zone)
  failure mode.
- **Recovery.** Run Q0 + Q4 of the
  [`decision tree`](decision-tree.md) explicitly. If the work
  genuinely needs Quadrant 4 in operation, record the
  Phase-crossing decision: *"new patterns are routed back to design
  as feedback"* or *"new patterns are handled in the loop in
  place, accepting the recurring attribution gap."* Both are
  admissible. If neither is acceptable, the work belongs in Q3
  (LLM Workflow Quadrant) and the autonomous loop should be
  removed from the operation surface.

## Operation patterns absorbed in place instead of routed to design

> "We hit a new edge case last week — the agent figured it out."

- **Failure mode.** New patterns surfacing in operation are
  absorbed by the autonomous loop's runtime instead of being
  surfaced as design-phase feedback. ADR-0010 calls the explicit
  routing-back-to-design path the *recommended* response; this
  anti-pattern is the alternative being chosen by default rather
  than by decision.
- **Why it happens.** Autonomous loops appear to "just handle"
  novel cases, which is what the architecture is for. The
  problem is that this masks design feedback that would have
  surfaced an upstream fix.
- **What breaks.** Layer 3 of the three-layer diagnosis (phase
  conflation): design and operation become indistinguishable in
  practice. Teams stop maintaining the design phase as a separate
  activity. Future workflow improvements stop happening because
  every pattern is "already handled" by the runtime.
- **Recovery.** Make the routing-back-to-design path explicit and
  log it. New patterns surfaced in operation are tagged, escalated
  to a design-phase review, and either encoded into the existing
  workflow (LLM Workflow Quadrant) or accepted as a domain that
  legitimately requires in-place handling (recorded as such).

## Skill-internal phase descent ignored — uniform ReAct or uniform freeze

> "Every step in this skill calls the LLM" / "Every step in this
> skill is a hardcoded path."

- **Failure mode.** A single skill is implemented as either *all
  runtime LLM judgment* (every subcomponent is a free-form LLM
  call, even when the targets sit at fixed paths and the scale is
  modest) or *all frozen pipeline* (every subcomponent is a
  hardcoded path, even when the targets vary across invocations
  and runtime judgment would actually be required). The
  [skill-design gradient](../glossary.md#skill-design-gradient)
  introduced in essay 7 (2026-05-02) is collapsed into a binary
  choice rather than evaluated subcomponent by subcomponent.
- **Why it happens.** Teams design skills with the implicit binary
  "is this a 'LLM-judgment skill' or a 'non-LLM script'?" instead
  of recognizing that the
  [Phase descent](../glossary.md#phase-descent) reaches inside
  the skill itself. Vendor framings ("agentic skills" vs
  "deterministic skills") reinforce the binary.
- **What breaks.**
  - *Uniform ReAct shape.* Subcomponents that would naturally be
    Quadrant 3 (or even Quadrant 1) get smuggled into operation as
    Quadrant 4 fragments. The
    [`attribution gap`](../glossary.md#attribution-gap) appears
    inside an otherwise transparent skill, and ADR-0010's
    Phase-crossing decision is bypassed at the skill-internal
    level even when the skill is nominally an operation-phase
    component.
  - *Uniform frozen-pipeline shape.* Subcomponents whose targets
    actually vary across invocations (low
    [target identifiability](../glossary.md#target-identifiability))
    or whose scale exceeds LLM miss-rate tolerance
    ([scale-resilience](../glossary.md#scale-resilience))
    silently fail when the assumed fixed paths or naming
    conventions stop holding. The skill returns wrong answers
    instead of acknowledging the runtime variance.
- **Recovery.** Walk each subcomponent through the
  [`decision tree`](decision-tree.md) using a lightweight
  skill-internal Q0 ("what phase is this subcomponent in — design
  or operation?"), then check the two secondary forces (target
  identifiability and scale-resilience) for that subcomponent. The
  full decision tree does not need to re-run inside every skill,
  but the subcomponent gradient position should be deliberate
  rather than inherited from the skill's surface label. When a
  subcomponent must run as runtime judgment inside an
  operation-phase skill, treat the choice as informational under
  ADR-0010's Skill-design descent: name what the subcomponent is
  doing and why a frozen-pipeline form will not work.

## Bounded work on autonomous loop

> "Implement customer support / FAQ classification / invoice matching
> as a ReAct agent."

- **Failure mode.** LLM Workflow Quadrant work routed through an
  autonomous-loop architecture. The 2026-04-30 essay's central
  diagnosis.
- **Why it happens.** Industry vocabulary lacks a positive name for
  the LLM Workflow Quadrant. "Anything that isn't deterministic" gets
  mapped to "agent" by elimination. Marketing and tooling reinforce
  the autonomous-loop default.
- **What breaks.** Component-level redirect that should have worked
  no longer works. Each LLM call's role is decided at runtime, so
  failures cannot be isolated to a specific component. The team
  assumes the wrong quadrant's accountability properties — assuming
  redirect is possible when it has been foreclosed.
- **Symptoms.** Sandbox strength keeps escalating. Human-in-the-loop
  load grows because operators are absorbing judgments the system
  cannot redirect. Postmortems land on "the agent did something
  weird" rather than naming a specific component.
- **Recovery.** Re-run the decision tree, especially Q3. The work
  has a definable workflow; build it as one. Replace the autonomous
  loop with deterministic control flow plus bounded LLM calls (one
  call per role, with a documented schema). Apply LLM Workflow
  Quadrant ADRs.

## OR problem on autonomous loop

> "Implement scheduling / routing / resource allocation as an LLM
> agent."

- **Failure mode.** Algorithmic Search Quadrant work routed through
  an autonomous-loop architecture. A category error.
- **Why it happens.** "We need an AI for this" is read as "we need
  an LLM," and combinatorial-search work that classical algorithms
  solve well is shoehorned into a loop.
- **What breaks.** Solution quality is worse than classical search.
  The loop hallucinates feasibility. There is no objective function;
  the LLM optimizes against an implicit goal that drifts. Worse,
  these failures inherit the Autonomous Agentic Loop Quadrant's
  attribution gap.
- **Recovery.** Q1 (deterministic?) → Yes. Q2 (search space static
  and finite?) → No. Algorithmic Search Quadrant. Use a classical
  solver. The LLM may have a place upstream (parsing intent,
  generating a structured problem instance) but the search itself
  belongs to the classical algorithm.

## Deterministic task on autonomous loop

> "Implement form-filling / data normalization as an agent."

- **Failure mode.** Script Quadrant work routed through an
  autonomous-loop architecture.
- **Why it happens.** "Agentify all the things" pitches the
  autonomous loop as a universal solvent. Work that classical scripts
  handle in milliseconds is reimplemented as a loop that takes
  seconds and costs more.
- **What breaks.** Cost, latency, and reliability all degrade with
  no benefit. Determinism is lost (the loop produces different
  outputs across runs). Audit becomes harder than the script it
  replaced.
- **Recovery.** Q1 → Yes. Q2 → Yes. Script Quadrant. Use a script
  or pipeline.

## Ungoverned autonomous loop in high-risk domain

> "Deploy an autonomous research/legal/medical/financial agent without
> answering Q4."

- **Failure mode.** Autonomous Agentic Loop Quadrant work running
  without a pre-named gap-bearer. The configuration ADR-0009 is
  designed to prevent.
- **Why it happens.** The triage decision is treated as
  configuration rather than as a structural commitment. "We'll
  figure out responsibility post-incident" is the implicit (and
  forbidden) plan.
- **What breaks.** The [*moral crumple zone*](../glossary.md#moral-crumple-zone)
  failure mode (Elish 2019): responsibility lands on whoever is
  operationally proximate — typically the operator with the least
  actual control over the loop's runtime decisions — rather than on
  a party who committed to bear the gap.
- **Recovery.** Run Q4 explicitly. Either name an
  organizationally identifiable gap-bearer (per ADR-0008 +
  ADR-0009), or *do not deploy*. If the work genuinely requires the
  Autonomous Agentic Loop Quadrant and no gap-bearer can be named,
  the deployment is infeasible.

## Approval gate as runtime checkbox

> "We have a Human Approval Gate — the user clicks 'OK' before each
> action."

- **Failure mode.** ADR-0005 reduced to a signpost in the sense of
  ADR-0001's critique.
- **Why it happens.** Approval gates are confused with confirmation
  dialogs. The latter habituates; the former requires a named human
  to inspect and decide.
- **What breaks.** Operators click through. The chain that should
  terminate at a human's decision instead terminates at "user
  pressed enter." Post-incident review finds no actual sign-off.
- **Recovery.** ADR-0005 specifies that the approval is on
  *behavior-modifying changes*, not on each runtime action; that
  the approver is a *named human*; and that the gate cannot be
  disabled. Behavior-modifying changes are the right granularity
  (changes to prompts, schemas, role definitions, routing logic).
  Runtime confirmations belong to a different mechanism if needed.

## Audit log as sample

> "We log a sample of agent runs for review."

- **Failure mode.** ADR-0006 reduced to a probabilistic
  observability practice. Causal traceability requires *every*
  event to be reconstructible.
- **Why it happens.** Logging cost concerns; "who would read all
  of it?" The audit log is treated as monitoring data rather than
  as the structural artifact ADR-0006 names it.
- **What breaks.** When an incident happens, the relevant run is
  not in the sample. Reconstruction fails on the case that actually
  matters. Sample-based logging is monitoring, not audit.
- **Recovery.** Append-only logging on every event. Storage cost is
  paid against the cost of post-incident reconstruction failure;
  the latter is much higher when the deployment is in the
  Autonomous Agentic Loop Quadrant.

## Treating quadrant as agent type

> "We're building an LLM Workflow Quadrant agent / a ReAct agent."

- **Failure mode.** Treating the quadrants as agent categories
  rather than work categories.
- **Why it happens.** Vendor pitches ("our autonomous agent...") and
  product framing ("the X agent") encourage thinking of the agent
  as a typed entity.
- **What breaks.** The same agent process can be deployed against
  different quadrants of work. Locking the agent to a quadrant
  hides the triage. When the agent is asked to handle work in a
  different quadrant, the architecture mismatch is invisible.
- **Recovery.** The quadrants describe *the work*. Run the decision
  tree on each piece of work the agent is asked to handle.
  Different quadrants require different applicable-ADR sets even
  for the same agent process.

## Skipping the triage when "obviously needs an agent"

> "It's obviously an autonomous task — let's just build the loop."

- **Failure mode.** Q3 not actually answered. The intuition that
  "this is an agent task" replaces the workflow-definability check.
- **Why it happens.** Cognitive shortcut. The triage feels
  bureaucratic when the answer seems obvious.
- **What breaks.** Half the work that "obviously needs an agent"
  turns out to be definable as a workflow once Q3 is asked
  explicitly. Skipping the triage installs the autonomous loop
  prematurely and pays the attribution-gap cost without need.
- **Recovery.** Always answer Q3 by drawing the workflow on a
  whiteboard *before* concluding it is undefinable. If you can draw
  it, the work is in the LLM Workflow Quadrant.

## Common thread

Most of these anti-patterns share a structure: a piece of work whose
quadrant has a *better* architectural fit gets routed to a *worse*
fit because the worse fit is the salient default in current
discourse. The decision tree exists to interrupt that salience —
walk the four questions explicitly, and the better fit usually
emerges.
