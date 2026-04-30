Language: English | [日本語](0010-phase-separation.ja.md)

# ADR-0010: Phase Separation Between Design and Operation (experimental)

> **Summary.** *Phase* (design vs operation) and *Quadrant*
> (problem-space) are independent dimensions of a deployment.
> Both phases can host components from any Quadrant, so this ADR is
> not a phase-to-quadrant mapping. The judgment it records is
> narrower: **placing an Autonomous Agentic Loop Quadrant component
> as the operation-phase default route a runtime-driven attribution
> gap into the production environment**, where ADR-0009's design-time
> commitment alone is no longer sufficient. Such placements remain
> admissible, but call for a documented Phase-crossing decision in
> addition to ADR-0009's gap-bearer naming.

## Status
**experimental** — the design / operation distinction is well
established outside the agent context (development vs production,
as-is vs operation, design-time vs runtime), but its translation
into the AAP Quadrants is recent (2026-05-01) and the operationally
measurable signals for the *operation-phase default* are still being
refined. See open questions at the end.

## Date
2026-05-01 (first formalization in this repository)

## Context

ADR-0009 ([Triage Before Autonomy](0009-triage-before-autonomy.md))
answered "should this work adopt an autonomous-loop architecture at
all?" by routing along the [Business AI
Quadrants](../glossary.md#business-ai-quadrants). The 2026-05-01
essay surfaces a related but distinct question: *if the answer is
yes, where in the lifecycle does the autonomous loop live, and does
that placement carry costs the design-time triage missed?*

The essay's argument is offered as observation and proposal, not as
a categorical mapping. The phrasing throughout — "isn't it really
the case that…", "couldn't this be released…" — is diagnostic, not
prescriptive. AAP picks up that posture: the rule below is narrow,
the surrounding context is *thinking-stimulus* for the deploying
team.

### Phase and Quadrant are independent

| | Design phase | Operation phase |
|---|---|---|
| Script Quadrant | prototype scripts | production pipelines |
| Algorithmic Search Quadrant | algorithm prototyping | production solvers |
| LLM Workflow Quadrant | prototype workflows in design reviews | production-default LLM workflows |
| Autonomous Agentic Loop Quadrant | coding agents, Deep Research, exploratory R&D | continuously-evolving research operations, exploratory analytics |

Every cell exists in real systems. A team building a customer-support
chat workflow uses the LLM Workflow Quadrant in *design phase*
(prototyping the routing prompt) before promoting it into the
*operation phase*. A research operation continuously surfacing new
investigation paths legitimately runs an Autonomous Agentic Loop in
*operation phase*. The 4×2 matrix is not a 1-to-1 mapping; it is a
landscape.

The narrow judgment AAP records is not "Quadrant N is the X-phase
tool." It is: *the operation-phase placement of the Autonomous
Agentic Loop Quadrant has a structural cost ADR-0009's design-time
commitment alone does not cover.*

### Why operation-phase autonomous loops are the load-bearing case

ADR-0009's gap-bearer naming addresses the attribution gap *as a
deployment commitment*. It works because ADR-0009 was framed at
design time: at the moment of architectural choice, the deployer
acknowledges the gap and names a subject who carries it.

Operation-phase placement adds a second-order risk on top: not just
"the gap exists and someone bears it," but "**the gap fires inside
production runtime, against unanticipated patterns, while the
gap-bearer is not in the loop**." The 2026-05-01 essay's central
observation: when a new pattern surfaces in operation, an
autonomous-loop architecture absorbs it dynamically — and that
dynamic absorption animates the runtime with the same blending that
forecloses redirect. The artificial redirect impossibility of
ADR-0009 reappears, this time inside the deployed system rather than
as a design-time mismatch.

This is what the deploying team should think about, not blanket
phase-to-quadrant mapping.

### The three-layer diagnosis (essays 4–6)

| Layer | Diagnosis | Essay |
|---|---|---|
| Surface | Quadrant misapplication — (3) work routed through (4) architecture | Essay 4 (2026-04-29) |
| Middle | Vocabulary gap — no positive name for the LLM Workflow Quadrant | Essay 5 (2026-04-30) |
| Deep | Phase conflation — "always-on autonomous agent runs business" compresses design + operation into one system | Essay 6 (2026-05-01) |

Misapplication grows from vocabulary gap; vocabulary gap from phase
conflation. ADR-0010 records the deepest layer as a *diagnostic
frame*, not a partitioning rule.

## Decision (experimental)

The Phase / Quadrant landscape is the deployment's design space.
Within it, AAP records the following narrow judgment:

1. **Operation-phase Autonomous Agentic Loop placements require an
   explicit Phase-crossing decision in addition to ADR-0009's
   gap-bearer naming.** The Phase-crossing decision answers, in one
   sentence: *when a new pattern surfaces in operation, will the
   autonomous loop handle it dynamically in place, or will it be
   routed back to the design phase as feedback?* Both answers are
   admissible. "We'll figure it out when it happens" is not.

2. **The default operation-phase composition is Quadrant 1 + 3 (+ 2
   where applicable).** This is a default, not a prohibition. It
   reflects an empirical observation: most workflows whose path is
   knowable at design time are better served by a composition that
   makes redirect succeed at the component level. Deviations from
   the default are admissible if (1) is satisfied.

3. **Cross-phase use of any Quadrant is normal.** The same Quadrant
   can appear in design and operation phases of the same project —
   a workflow prototyped (design) and then promoted (operation), or
   an autonomous loop used to explore (design) before its findings
   are encoded into a workflow (operation). The judgment is per
   placement, not per Quadrant.

4. **Feedback path is the recommended response to new operation-phase
   patterns.** When a new pattern surfaces in operation and (1)
   was decided as "route to design," the handoff is explicit: tag
   the pattern, escalate to design phase, and return the operation
   surface to the default composition until the design phase decides
   where the new pattern fits in the Quadrants. This is a
   recommendation, not a hard requirement; the alternative
   (in-place dynamic handling) is admissible under (1) and is
   explicitly chosen.

When these conditions are not named, the default in (2) applies:
the operation deployment runs on Quadrant 1 + 3 (+ 2) components,
and Autonomous Agentic Loop placements that drift into operation
without an explicit Phase-crossing decision are out of compliance
with ADR-0010.

### What this is *not* saying

- **It is not saying "Autonomous Agentic Loop is for design phase
  only."** Operation-phase placements are admissible. They carry
  the additional commitment in (1).
- **It is not saying "LLM Workflow Quadrant is for operation phase
  only."** Design-phase prototyping with bounded LLM calls is
  routine and legitimate.
- **It is not partitioning Quadrants by Phase.** The Phase /
  Quadrant landscape is two-dimensional; this ADR records a single
  load-bearing observation about one cell of it (operation-phase
  Quadrant 4) and a default about another (operation-phase
  composition).
- **It is not banning anything.** It is making the team's
  Phase-crossing decision *explicit instead of implicit*. The
  rule is procedural, not architectural.

### What this permits

- **The same workflow appearing in both phases.** A workflow whose
  routing prompt is prototyped in design and promoted to operation
  is the normal lifecycle, not a Phase boundary violation.
- **Operation-phase Autonomous Agentic Loop with the
  Phase-crossing decision recorded.** This is admissible. The
  deployment is accountable for the cost it has named.
- **Design-phase residents that look like deployed services.**
  Coding agents, Deep Research tools, exploratory browsing agents
  — these run continuously, but each session is an independent
  design session that completes when its exploration finishes.
  Their continuous availability is a deployment-level fact, not a
  phase-level property of each session.

### What this does not permit

- **Implicit Phase-crossing decisions.** Operation-phase
  Autonomous Agentic Loop placements without a documented
  Phase-crossing decision recorded at deployment time are out of
  compliance.
- **Operation-phase autonomous loops without an ADR-0009
  gap-bearer.** ADR-0009 already requires this; ADR-0010 does
  not relax it.

## Alternatives Considered

- **Strict ban: autonomous loop never enters operation phase.**
  Rejected. Records and observations show legitimate edge cases
  (high-frequency exploratory analytics, continuously-evolving
  research operations) where the design / operation boundary
  blurs. A categorical ban writes those cases out of the
  framework rather than naming the cost they pay.
- **Phase-to-Quadrant 1-to-1 mapping (Autonomous Loop = Design
  phase, LLM Workflow = Operation phase).** Rejected as the
  earlier draft of this ADR did. Real systems put Quadrant N in
  both phases routinely; a 1-to-1 mapping forces them out of
  the framework. The mapping is a landscape, not a partition.
- **Strong guardrails are sufficient.** Rejected as a substitute
  for the Phase-crossing decision. Guardrails (sandbox strength,
  HITL load) bound blast radius and add review; they do not
  surface the team's choice between in-place absorption and
  feedback-to-design as an explicit deployment-time decision.
- **Subsume Phase Separation into ADR-0009.** Rejected. ADR-0009
  triages whether the work belongs in the Autonomous Agentic Loop
  Quadrant at all. ADR-0010 surfaces the Phase-crossing decision
  for placements where ADR-0009's answer is yes. They are
  independent triage axes (problem-space and time-axis); merging
  them loses both clarity and the diagnostic-frame value of the
  three-layer analysis.

## Consequences

- The repository's structure becomes **10 ADRs + 4 Business AI
  Quadrants**, where ADR-0009 and ADR-0010 form a *triage pair*:
  0009 asks "which Quadrant?", 0010 surfaces the Phase-crossing
  decision when the answer is the Autonomous Agentic Loop Quadrant.
- Coding agents, Deep Research, and similar always-on exploratory
  systems are explicitly named as design-phase residents
  *deployed as services*. Their legitimacy under AAP is preserved
  without forcing them into a category exception.
- Operation-phase deployments default to a transparent composition
  (Quadrant 1 + 3 + 2 where applicable), where redirect succeeds
  at the component level. Deviations are admissible and explicit.
- Design-phase use of LLM Workflow Quadrant or Autonomous Agentic
  Loop Quadrant is normal and not flagged.
- Post-incident reconstruction can answer not only "what did the
  agent do?" but also "what Phase-crossing decision was the
  deployment running under when this happened?" The answer is in
  the deployment record, not inferred.
- The three-layer diagnosis (essays 4–6) becomes a permanent
  diagnostic frame for design reviews: when an autonomous loop is
  proposed for operation, walk the layers. Most resolutions
  surface a misapplication (layer 1) or a vocabulary gap
  (layer 2) before the phase question even arises.

## Open questions (why this is experimental)

1. **Operation-phase Autonomous Agentic Loop edge cases.**
   High-frequency exploratory analytics, continuously-evolving
   research operations, real-time content moderation against
   unbounded patterns — these are domains where the
   Phase-crossing decision "handle in place" may be the
   structurally honest answer. AAP does not have empirical signals
   yet for distinguishing "Phase-crossing in place is right here"
   from "this team is rationalizing a misapplication."
2. **Sequence-of-design-sessions framing.** When does a coding
   agent or Deep Research tool stop being a sequence of independent
   design sessions and become an operation-phase deployment? The
   structural criterion (each session completes; state does not
   accumulate across them in operation-relevant ways) is open to
   interpretation in vendor-built systems.
3. **Recursion.** When the design phase itself is autonomous
   (the agent designs its own next experiment), does the rule
   recurse? The boundary between "design session" and "design
   system" is unresolved.
4. **Interaction with ADR-0009 in operation-phase placements.**
   Does the Phase-crossing decision constitute a *second*
   commitment alongside the gap-bearer (one for the attribution
   gap, one for the phase-crossing risk), or is the same subject
   both? In practice, the same human / role is usually both;
   whether a structural separation is needed is open.
5. **Vendor framing.** Most current autonomous-loop products are
   marketed as operation-phase deployments. AAP-aligned deployments
   need to make the Phase-crossing decision explicit even when the
   vendor materials assume "operation = autonomous-loop runtime"
   by default. Whether this requires a counter-vocabulary AAP
   should provide is open.

This ADR will be revised as operational experience accumulates with
the Phase-crossing decision in actual deployments. The status will
move from experimental to accepted once the edge cases have
documented answers, or to rejected / superseded if the design /
operation distinction turns out not to be the right cut.

---

**Lineage.** The 2026-05-01 essay
([Is ReAct Needed in Production? — Separating Design and Operation
Phases](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant-3.md))
articulated the design / operation distinction and the three-layer
diagnosis (misapplication → vocabulary gap → phase conflation). The
Japanese original is at
[`articles/react-agent-business-quadrant-3.md`](https://github.com/shimo4228/zenn-content/blob/main/articles/react-agent-business-quadrant-3.md).
The argument is offered as observation and proposal, not as a
categorical phase-to-quadrant mapping; ADR-0010 preserves that
posture. ADR-0010 sits parallel to
[ADR-0009](0009-triage-before-autonomy.md): 0009 triages whether the
work adopts the autonomous loop; 0010 surfaces the Phase-crossing
decision when 0009's answer is yes and the placement is
operation-phase. The earlier observation that the design / operation
distinction exists outside the agent context (development vs
production, as-is vs operation, design-time vs runtime) is what
gives the cut its structural weight; the agent context did not
invent the cut, it imported it.
