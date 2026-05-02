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

### contemplative-agent (implementation)

[contemplative-agent](https://github.com/shimo4228/contemplative-agent)
is the running implementation that the eight ADRs were extracted from.
Each principle in this repository corresponds to one or more ADRs in
`contemplative-agent/docs/adr/`, typically with project-specific
details that were removed during extraction here:

| Principle in this repo | Corresponding contemplative-agent ADR |
|------------------------|---------------------------------------|
| ADR-0001 Security by Absence | [ADR-0007 Security Boundary Model](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0007-security-boundary-model.md) (partially) |
| ADR-0002 Deterministic Prohibition at the Scaffolding Layer | PreToolUse hooks (`block-episode-logs-{read,bash,grep}.sh`) + `summarize_record` quarantine in [contemplative-agent](https://github.com/shimo4228/contemplative-agent); rationale documented in `docs/security/2026-04-01-episode-log-access-control.md` and the ADR-0021 implementation audit (ADR-0028 / ADR-0029) |
| ADR-0003 Untrusted Content Boundary | [ADR-0007 Security Boundary Model](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0007-security-boundary-model.md) (partially) |
| ADR-0004 Single External Adapter | [ADR-0015 One External Adapter Per Agent](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0015-one-external-adapter-per-agent.md) |
| ADR-0005 Human Approval Gate | [ADR-0012 Human Approval Gate](https://github.com/shimo4228/contemplative-agent/blob/main/docs/adr/0012-human-approval-gate.md) |
| ADR-0006 Causal Traceability | Implicit across the codebase; first named in the 2026-04-13 essay |
| ADR-0007 Scaffolding Visibility | Implicit in the fully-materialized scaffolding design; first named in the 2026-04-14 essay |
| ADR-0008 One Agent, One Human | First formalized in this repository (experimental) |
| ADR-0009 Triage Before Autonomy | First formalized in this repository (experimental); essays 4 and 5 are its lineage |
| ADR-0010 Phase Separation Between Design and Operation | First formalized in this repository (experimental); essays 6–7 are its lineage |

### agent-knowledge-cycle (AKC)

[AKC](https://github.com/shimo4228/agent-knowledge-cycle) (DOI
`10.5281/zenodo.19200727`) is a sibling project that captures the
*mechanism* side of what contemplative-agent produces: the six-phase
knowledge cycle (Research → Extract → Curate → Promote → Measure →
Maintain) that keeps an agent's skills, rules, and docs aligned with
reality over time.

In v1.x, AKC included three ADRs that turned out to be genre-specific
(Security by Absence / Single External Adapter / Untrusted Content
Boundary) rather than mechanism-neutral. AKC v2.0.0 (2026-04-19) extracted
them into `_archive/akc-security-triplet-2026-04/`, with the archive
README noting they would "be re-expressed... in a sibling genre library
at a later date." *This repository is that sibling.*

The relationship:

- **AKC**: the cycle (mechanism). Genre-neutral. How knowledge flows.
- **This repository**: the constraints (content, for autonomous AI
  agents). How accountability is distributed.

Both are extractions from contemplative-agent, along different axes.

## Industry artifacts that instantiate AAP principles

Through 2026 Q2, several industry releases shipped the *mechanism
layer* that AAP's principles describe. They are listed here not as
reference architectures and not as endorsed implementations, but as
concrete instances readers can use to ground the abstract ADR
language. The repository's core thesis — *implementation dissolves;
judgment persists* — remains the framing: each artifact below is a
mechanism instance for a principle this repository records as a
judgment. The judgment layer (who should be the sponsor, what
commitment that implies, the difference between artificial and
principled redirect impossibility, the Phase-crossing decision in
ADR-0010) is not what the artifacts ship.

### Microsoft (2026 Q2)

A coordinated three-stage release populated the mechanism layer
across the Microsoft ecosystem:

- **[Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)**
  (2026-04-02, MIT-licensed open source). Sub-millisecond
  policy-engine gate (Agent OS) intercepting all agent actions, with
  OWASP Agentic Top 10 coverage. A direct mechanism instance for
  ADR-0002's deterministic prohibition at the scaffolding layer.
- **Microsoft Agent Framework 1.0** (2026-04-03). .NET / Python SDK
  unifying Semantic Kernel and AutoGen, with full MCP protocol
  support and A2A support announced at 1.0 (full A2A 1.0 support
  noted as forthcoming in the launch post), a graph-based workflow
  engine, and middleware hooks. Populates the LLM Workflow Quadrant
  tooling alongside Anthropic's "Building Effective Agents" patterns.
- **[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)**
  (GA 2026-05-01, $15/user/month standalone or part of Microsoft 365
  E7 — also referred to as the Frontier Suite). Four mechanism axes:
  Entra Agent ID (a dedicated identity category for agents alongside
  users and devices, with sponsor system) → mechanism instance for
  ADR-0008 binding; Purview Data Security Investigations (log
  correlation across Microsoft 365, Azure, and integrated third-party
  AI services) → mechanism instance for ADR-0006 causal traceability;
  cross-cloud registry sync (AWS Bedrock, Google Cloud, public
  preview) → an *observability* approach distinct from ADR-0004's
  structural blast-radius bounding; pricing that treats governance as
  standard cost rather than optional add-on. The "Shadow AI" framing
  Microsoft uses is the industry-level recognition of what ADR-0007
  Scaffolding Visibility names as the absence problem.

### AWS Bedrock + OpenAI integration (2026-04)

AWS announced OpenAI model availability through Bedrock on
2026-04-28 as a limited preview, with general availability
described as imminent at launch, strengthening Bedrock as an
independent agent-management surface. This is a mechanism-layer
instance separate from the Microsoft stack; the same judgment-layer
questions apply (who is the gap-bearer, what is the Phase-crossing
decision when an autonomous loop is placed in operation phase).

### Google Vertex AI Agent Builder (rebranded Gemini Enterprise Agent Platform at Google Cloud Next 2026)

Google's independent agent-platform line. Included as a sync target
in Microsoft's cross-cloud registry preview, demonstrating the
fragmentation problem entro.security flags: each cloud provider
maintains its own identity model, producing multiple governance
blind spots that a registry sync surfaces but does not eliminate.

### Identity-security startups (entro.security, oasis.security)

Targeting the cross-environment governance gap that Microsoft
Agent 365 cannot reach (GitHub Actions, Slack, Jira, Jenkins, other
non-Microsoft SaaS). Their core observation — *each cloud
provider builds its own agent identity model, producing fragmented
control and multiple governance blind spots* — sits in productive
tension with ADR-0004's Single External Adapter principle: their
mechanism is post-hoc observability over agents that have already
acquired multiple external surfaces; ADR-0004 is the upstream
design judgment that bounds the surface count at design time.
Both layers have value; the glossary entry
[Observability vs structural enforcement](glossary.md#observability-vs-structural-enforcement)
records the distinction.

### What the industry layer ships, and what it does not

The artifacts above ship *mechanism*: identity primitives for
agents, sub-millisecond policy gates, cross-vendor audit
infrastructure, sponsor systems, registry sync. They do not ship
*judgment*: who should be the sponsor and what commitment that
acknowledges, why the prohibition-strength hierarchy ranks absence
above scaffolding above untrusted boundary, when an autonomous loop
should be triaged out of operation phase, what Phase-crossing
decision a deployment is running under. The two layers are
complementary; the mechanism layer makes the principles technically
enforceable, the judgment layer records when, why, and at what cost
the principles should be enforced. AAP's harness-neutral,
vendor-neutral, DOI-registered artifact treatment is what allows
this complementarity to hold across the multiple mechanism
instances that have started to appear.

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
