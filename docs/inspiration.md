# Inspiration and Lineage

## The seven articles

The narrative spine of this repository is a seven-essay sequence
published in April–May 2026. The first three form the original
trilogy (problem / application / obstacle); the next four are the
architectural follow-up that introduced the four-quadrant
decomposition, named the principled vs artificial redirect
impossibility, added the design / operation phase distinction, and
descended that distinction to skill-design granularity:

1. **[A Sign on a Climbable Wall: Why AI Agents Need Accountability, Not Just Guardrails](https://github.com/shimo4228/zenn-content/blob/main/articles-en/ai-agent-accountability-wall-en.md)** (2026-04-06)
   > *Problem statement.* Signs on climbable walls are meaningless.
   > From Plato's Ring of Gyges (root privilege without audit log) through
   > Hobbes' reputation-based access control, to the three engineering
   > enforcement patterns (physical constraint / consequence / internalized
   > value) — the "signpost" pattern has been known to fail for 2400 years.
   > Current AI governance is in the signpost phase. What is needed is
   > accountability architecture.

2. **[Can You Trace the Cause After an Incident?](https://github.com/shimo4228/zenn-content/blob/main/articles-en/agent-causal-traceability-org-adoption-en.md)** (2026-04-13)
   > *Application.* Post-incident causal tracing requires build-time
   > structure. The cost asymmetry (incident cost >> steady-state cost)
   > pushes rational investment upstream. The structures that emerge —
   > segregation of duties, least privilege, four-eyes approval, audit
   > trails — are structurally identical to the practices organizations
   > have refined over centuries.

3. **[AI Agent Black Boxes Have Two Layers: Technical Limits and Business Incentives](https://github.com/shimo4228/zenn-content/blob/main/articles-en/agent-blackbox-capitalism-timescale-en.md)** (2026-04-14)
   > *Obstacle analysis.* Blackbox has two layers: model-weights
   > internalization (technical) and commercial secrecy (business). Both
   > block the transparency causal tracing requires. The resolution is
   > not "publish everything" or "hide everything," but defining the
   > minimum set that makes causal tracing possible.

4. **[Where ReAct Agents Are Actually Needed in Business](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant.md)** (2026-04-29)
   > *Architectural triage by quadrant.* Business AI sorts along two
   > axes (deterministic vs semantic-judgment, pre-defined workflow vs
   > exploratory) into four quadrants — Script, Algorithmic Search, LLM
   > Workflow, and Autonomous Agentic Loop. Most current LLM applications
   > belong to the LLM Workflow Quadrant, where deterministic control
   > flow with bounded LLM calls suffices. Forcing those workloads into
   > the Autonomous Agentic Loop Quadrant's architecture is a structural
   > source of much of the accountability collapse the trilogy diagnosed.

5. **[The LLM Workflow Quadrant Is Missing from Our Vocabulary](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant-2.md)** (2026-04-30)
   > *The vocabulary diagnosis and the principled attribution gap.*
   > The industry has no positive name for the LLM Workflow Quadrant,
   > so non-deterministic work is routed through the Autonomous Agentic
   > Loop by elimination. Even when the quadrant mismatch is corrected,
   > a deeper layer remains: Autonomous Agentic Loop Quadrant work
   > blends judgment elements at runtime, foreclosing post-hoc
   > separability — Elish's *moral crumple zone* applied to autonomous
   > agents. Distribution then bottoms out on a subject the legal
   > system does not yet recognize.

6. **[Is ReAct Needed in Production? — Separating Design and Operation Phases](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant-3.md)** (2026-05-01)
   > *The temporal axis.* Business work splits into a design phase and
   > an operation phase. Design demands flexibility (the path is
   > unknown); operation demands predictability (the path is known) —
   > their optimization axes are inverted. Compressing both phases
   > into one system is the deepest layer of the agent ecosystem's
   > confusion. The proposal: surface the Phase-crossing decision
   > explicitly when an autonomous loop is placed in operation, and
   > recommend routing new operation-phase patterns back to design as
   > feedback rather than absorbing them in place. The argument is
   > offered as observation and proposal, not prohibition.

7. **[Between the Workflow and ReAct Quadrants: How Phase Decides Skill Design](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant-4.md)** (2026-05-02)
   > *Phase descends to skill design.* The (3) LLM Workflow Quadrant and
   > (4) Autonomous Agentic Loop Quadrant aren't a clean dichotomy —
   > there's a continuous gradient between them. The Phase Separation
   > introduced in essay 6 (design vs operation) descends from the level
   > of business systems to individual skill design. Skill-equivalent
   > work lands at different positions on this gradient depending on
   > phase, with object specifiability (whether targets have fixed
   > paths) and scale-resilience as secondary forces. AKC's "capability
   > ↑ → holistic OK" principle treats neither.

Together, these seven form the argument in three layers. Surface
(essays 1–3): current AI governance is signpost-era; structural
accountability is what works; the obstacles are internalization
into weights and commercial opacity. Middle (essays 4–5): the
architectural triage is itself prior to the accountability question
— routing work to the wrong quadrant generates artificial
accountability problems that are resolvable by re-architecting,
while choosing the Autonomous Agentic Loop Quadrant commits the
deploying organization to a principled attribution gap that
requires a pre-named gap-bearer. Deep (essays 6–7): the
misapplication diagnosed at the surface grows from the vocabulary
gap diagnosed in the middle, which in turn grows from the *phase
conflation* of treating design and operation as the same activity.
Essay 7 extends the phase distinction from business systems down to
individual skill design, where the same axis decides whether a
sub-component is a script-frozen pipeline step or a runtime LLM
judgment.

## Related repositories

This file focuses on the **lineage of ideas** (the seven articles).
For the full sibling map and ADR-by-ADR correspondence, see
[`CLAUDE.md`](../CLAUDE.md) (working context with usage conventions)
and [`docs/CODEMAPS/architecture.md`](CODEMAPS/architecture.md)
(relation graph). The minimal pointers below exist only to keep the
articles' references resolvable in isolation.

- **[contemplative-agent](https://github.com/shimo4228/contemplative-agent)**
  is the running implementation the ADRs in this repository were
  extracted from. Project-specific details were removed during the
  re-expression here.

- **[agent-knowledge-cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle)**
  (DOI `10.5281/zenodo.19200726`) is a sibling project that captures
  the *mechanism* side of what contemplative-agent produces — the
  six-phase knowledge cycle (Research → Extract → Curate → Promote →
  Measure → Maintain). In v1.x AKC included three ADRs that turned
  out to be genre-specific (Security by Absence / Single External
  Adapter / Untrusted Content Boundary) rather than mechanism-neutral.
  AKC v2.0.0 (2026-04-19) extracted them into
  `_archive/akc-security-triplet-2026-04/`, with the archive README
  noting they would "be re-expressed... in a sibling genre library
  at a later date." *This repository is that sibling.* AKC = the
  cycle (how knowledge flows); this repository = the constraints
  (how accountability is distributed). Both are extractions from
  contemplative-agent, along different axes.

## Industry artifacts that instantiate AAP principles

Through 2026 Q2, several industry releases shipped the *mechanism
layer* that AAP's principles describe (Microsoft Agent 365 and the
surrounding Microsoft / AWS / Google releases, plus identity-security
startups). The per-artifact mapping to ADRs is maintained in
[`industry-mapping.md`](industry-mapping.md) — a deliberately
time-bound document that decays as vendor products evolve, kept
separate from the ADRs so the judgments themselves stay clean.

## Why the extraction path looked like this

The shortest version:

1. contemplative-agent was built to solve a specific problem (run a
   contemplative AI agent on a social platform).
2. Architectural ideas surfaced that turned out to matter structurally
   regardless of the problem.
3. Three were extracted into AKC v1.x because they co-occurred with the
   knowledge-cycle mechanism.
4. AKC v2.0.0 concluded that cycle-and-constraints are actually
   separable, and moved to mechanism-only.
5. This repository re-expresses the constraints plus the ones that had
   surfaced in the meantime (the three-article trilogy, the
   1-agent-1-human experiment, and the deterministic-prohibition
   hierarchy that emerged from the MINJA / episode-log access-control
   work).

The re-expression required rewriting each ADR to be harness-neutral.
The original decisions remain dated to when they were first made;
the re-expression dates the placement in this repository.
