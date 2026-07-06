Language: English | [日本語](case-studies.ja.md)

# Case Studies — Quadrant Routing in Practice

> **Purpose.** Concrete examples of routing common business AI work
> to a quadrant — with the architecture, applicable ADRs, and (where
> relevant) typical phase placement spelled out. The cases below are
> *illustrative*, designed to stimulate the team's thinking, not to
> be pattern-matched. Phase ≠ Quadrant: the same Quadrant can appear
> in both design and operation phases, and the cases note which
> placement is *typical*, not *exclusive*.
>
> **Currency.** Each case carries a publication date. The choices
> reflect what is sensible given current LLM and tooling capability
> as of that date. Re-evaluate when capabilities shift.

## (Autonomous Agentic Loop Quadrant, *Typical placement: Design phase*) Coding agent

*Published 2026-05-01.*

- **Work.** A developer asks a coding agent to investigate, modify,
  test, and commit code in a repository. Each session ends when the
  task is complete.
- **Phase.** Design phase resident. Even when the agent is "always
  on" (Devin, GitHub Copilot Coding Agent in CI/CD), each session is
  an *independent design session* that completes when its
  exploration finishes; state does not accumulate across sessions in
  operation-relevant ways.
- **Quadrant.** Autonomous Agentic Loop Quadrant.
  - Q0 (phase?) — Design.
  - Q1 (deterministic?) — No.
  - Q3 (workflow definable?) — No; the next action depends on what
    the previous file read or test result returned.
  - Q4 (gap-bearer + Phase-crossing) — Both must be answered. The
    gap-bearer is typically the developer who initiated the
    session; for unattended CI runs, the gap-bearer is the
    repository owner. The Phase-crossing decision is automatic
    (this *is* the design phase).
- **Architecture.** ReAct-style autonomous loop with a controlled
  tool set (file read, file edit, shell, test runner) and an
  explicit termination criterion (task complete / approval gate).
- **Applicable ADRs.** All ten. ADRs 0005, 0006, 0008, 0009 carry
  weight; ADR-0010's Phase-crossing condition is satisfied
  automatically.
- **Why this is *not* an exception to ADR-0010.** Coding sessions
  are sequences of independent design sessions. Their continuous
  availability is a deployment-level fact, not a phase-level fact.
  Treating coding agents as operation-phase deployments is the
  category error ADR-0010 names.

## (Autonomous Agentic Loop Quadrant, *Typical placement: Design phase*) Deep Research for ad-hoc analysis

*Published 2026-05-01.*

- **Work.** A user submits a research question whose answer requires
  iterative search, retrieval, and synthesis across an unbounded
  knowledge corpus. Each query ends when the synthesis is complete.
- **Phase.** Design phase resident (per-query design session).
- **Quadrant.** Autonomous Agentic Loop Quadrant.
- **Architecture.** ReAct-style autonomous loop with retrieval
  tools (search, fetch, cite) and a synthesis step.
- **Applicable ADRs.** All ten. The session-level gap-bearer is
  the user (or the user's employer for paid deployments); the
  Phase-crossing decision is automatic.
- **Why this is *not* operation-phase.** A query session is
  exploratory; the next search depends on what the previous result
  returned. The deployment's appearance of being "operation phase"
  (the service is always available) is a delivery property, not a
  phase property.

## (LLM Workflow Quadrant **batch sub-form**, *Typical placement: Operation phase*) FAQ classification

*Published 2026-04-30.*

- **Sub-form.** Batch sub-form: the pipeline owns the control flow;
  the LLM is invoked once per question with a fixed role
  (`classify(question) -> category`).
- **Work.** A user submits a question; the system classifies it into
  one of N FAQ categories and returns the matching answer.
- **Phase.** Operation phase. Default composition (no Phase-crossing
  decision needed; Quadrant 4 is not in the composition).
- **Quadrant.** LLM Workflow Quadrant.
  - Q1 (deterministic?) — No, semantic judgment required.
  - Q3 (workflow definable?) — Yes; one classification call, one
    lookup, one return.
- **Architecture.** Single-purpose LLM function: `classify(question)
  -> category` with a documented schema. Followed by a deterministic
  lookup `category -> answer`. No autonomous loop.
- **Applicable ADRs.** 0001, 0003, 0006, 0007 (full list in
  [`governance-mapping.md`](governance-mapping.md)).
- **Redirect path.** If the classification is wrong, the role owner
  of `classify()` (model selection, prompt design) is the redirect
  target.
- **Anti-pattern.** Implementing as an autonomous agent that "decides
  which FAQ to retrieve." See
  [`anti-patterns.md#bounded-work-on-autonomous-loop`](anti-patterns.md#bounded-work-on-autonomous-loop).

## (LLM Workflow Quadrant **conversational sub-form**, *Typical placement: Operation phase*) Customer support specialist chat

*Published 2026-04-30.*

- **Sub-form.** Conversational sub-form: the human in the
  conversation is the *judging agent*; each turn is a bounded LLM
  call (system prompt + retrieval + history) whose role is fixed by
  the surrounding harness.
- **Work.** A multi-turn chat with a user, grounded in a product
  knowledge base. Escalates to a human when the conversation reaches
  a topic outside the scope.
- **Phase.** Operation phase. Default composition.
- **Quadrant.** LLM Workflow Quadrant (multi-turn but bounded — see
  the boundary discussion below).
  - Q1 (deterministic?) — No.
  - Q3 (workflow definable?) — Yes: each turn is a bounded LLM call
    (system prompt + retrieval + history) followed by an escalation
    check.
- **Architecture.** Specialist chat agent: a single LLM call per
  turn with a fixed role (answer questions about product X using
  retrieved context). The escalation check is itself a bounded
  classification call.
- **Applicable ADRs.** 0001, 0003 (RAG content is untrusted), 0006,
  0007.
- **Redirect path.** Wrong answer → role owner of the answer call
  (RAG curator if the wrong document was retrieved; prompt designer
  if the prompt encouraged the wrong synthesis).
- **Boundary note.** Multi-turn chat agents sit on the boundary
  between (3) and (4) — see ADR-0009 open question 1. The test:
  does the role of each turn's LLM call stay bounded under unusual
  user input, or does the agent start "deciding what to do next"
  beyond answering questions about product X? If the role bound
  holds, this is (3). If not, it has drifted into (4) and the
  ADR-0009 triage applies.

## (LLM Workflow Quadrant **batch sub-form**, *Typical placement: Operation phase*) Invoice matching

*Published 2026-04-30. Source: 2026-04-29 essay.*

- **Sub-form.** Batch sub-form: the pipeline (`process_invoice()`)
  owns the control flow; a single-purpose LLM function
  (`match_line_items(...) -> Verdict`) is invoked at the one
  semantic-judgment point.
- **Work.** Match incoming invoices against purchase orders;
  approve, escalate, or reject.
- **Phase.** Operation phase. Default composition.
- **Quadrant.** LLM Workflow Quadrant.
  - Q1 (deterministic?) — Mostly yes (PO existence, expiry,
    duplicate, exact-match amount), but the line-item matching step
    requires semantic judgment.
  - Q3 (workflow definable?) — Yes; control flow is a deterministic
    pipeline with one LLM call at the line-item-matching point.
- **Architecture.** Deterministic pipeline (`process_invoice()` in
  the source essay) with a single-purpose LLM function
  `match_line_items(invoice_lines, po_lines) -> Verdict`.
- **Applicable ADRs.** 0001, 0003, 0005 (changes to the matching
  prompt/schema go through approval), 0006, 0007.
- **Redirect path.** A wrong match isolates to either the LLM
  function's role owner or the pipeline's logic owner. Both are
  identifiable.

## (Autonomous Agentic Loop Quadrant, *Typical placement: Design phase*) Research assistant for open-ended literature exploration

*Published 2026-04-30; phase note added 2026-05-01.*

- **Work.** A user submits a research question whose answer requires
  iteratively searching, reading, and synthesizing across a
  knowledge corpus whose structure cannot be enumerated in advance.
- **Phase.** Design phase resident (per-query design session, like
  the Deep Research case above; included separately because it
  documents the team-deployed variant rather than the consumer-tool
  variant).
- **Quadrant.** Autonomous Agentic Loop Quadrant.
  - Q0 (phase?) — Design (per query).
  - Q1 (deterministic?) — No.
  - Q3 (workflow definable?) — No; the next search depends on what
    the previous search returned. Bounding the call's role would
    prevent the work.
- **Architecture.** ReAct-style autonomous loop (Thought → Action →
  Observation), with a controlled tool set (search, retrieve, cite)
  and an explicit termination criterion.
- **Applicable ADRs.** All ten. ADRs 0005, 0006, 0008, 0009
  load-bearing; ADR-0010's Phase-crossing decision is satisfied
  automatically (design phase).
- **Required commitments at deployment time.**
  - **ADR-0009.** The triage decision is recorded; the work is
    explicitly named as Autonomous Agentic Loop Quadrant work.
  - **ADR-0008.** The accountable operator is named. *Alice is the
    accountable operator for this research assistant deployment.*
  - **Pre-named gap-bearer.** Same operator (or a higher-level role
    if the work crosses individual authority).
  - **ADR-0005.** Behavior-modifying writes (changes to the loop's
    prompts, tools, or termination criteria) pass through Alice's
    approval gate. As a deployment-specific choice — stricter than
    the ADR floor — Alice also gates outward-facing actions
    (publishing the report, sending email, posting to a channel).
- **Redirect path.** Component-level redirect is foreclosed. If the
  report is wrong, Alice is the redirect target (per the gap-bearer
  commitment). Post-incident options: retrain, re-architect (move
  parts of the work to LLM Workflow Quadrant), retire.

## (Algorithmic Search Quadrant) Resource scheduling

*Published 2026-04-30.*

- **Work.** Allocate a finite set of resources (rooms, machines,
  staff) across a finite set of demands subject to constraints.
- **Quadrant.** Algorithmic Search Quadrant.
  - Q1 (deterministic?) — Yes; the constraints and objective are
    fully specifiable.
  - Q2 (search space static and finite?) — No; combinatorial
    enumeration is required.
- **Architecture.** Constraint solver, integer programming, or
  metaheuristic search. No LLM in the loop.
- **Out of scope for AAP.** Standard SE accountability applies; the
  algorithm author and the constraint specification owner are the
  redirect targets.
- **Anti-pattern.** Implementing as an autonomous agent that "asks
  an LLM to schedule." See
  [`anti-patterns.md#or-problem-on-autonomous-loop`](anti-patterns.md#or-problem-on-autonomous-loop).

## (Hybrid) Contract review

*Published 2026-04-30.*

- **Work.** Review a contract: extract clauses, check against a
  policy library, flag novel-pattern clauses for human attention.
- **Quadrant.** Hybrid.
  - **Clause extraction** — LLM Workflow Quadrant. Each clause type
    is a bounded call with a documented schema.
  - **Policy comparison** — LLM Workflow Quadrant. Each policy
    check is a bounded call.
  - **Novel-clause analysis** — Autonomous Agentic Loop Quadrant
    *only if* the analysis genuinely requires open-ended
    exploration of legal precedent. In practice, this part is often
    handed to a human rather than an autonomous loop, because the
    cost of a hallucinated precedent citation is high.
- **Architecture.** Pipeline of LLM Workflow Quadrant calls, with an
  approval-gated escalation to either a human or (with ADR-0008/0009
  satisfied) an Autonomous Agentic Loop for the novel-clause
  portion.
- **Applicable ADRs for the LLM Workflow portion.** 0001, 0003,
  0005, 0006, 0007.
- **Applicable ADRs for the Autonomous Agentic Loop portion (if
  used).** All ten.
- **Boundary discipline.** The handoff between the two portions is
  itself a structural choice and is logged. The Autonomous Agentic
  Loop portion has its own named gap-bearer, distinct from the
  workflow operator if the role separation matters.

## (Autonomous Agentic Loop Quadrant, with the operator-experience caveat) Knowledge-base copilot

*Published 2026-04-30. Source: implementation experience cited in
the 2026-04-29 essay.*

- **Work.** A vast official documentation set; users ask questions
  whose answers require traversing the knowledge graph in ways that
  cannot be planned in advance.
- **Quadrant.** Autonomous Agentic Loop Quadrant.
  - Q3 (workflow definable?) — No. The next search depends on
    observations. Bounding would prevent the work.
- **Architecture.** ReAct-style loop with retrieval tools.
- **Applicable ADRs.** All ten.
- **Reported operator experience.** Strong results on the work
  itself; reduced predictability under operation. The number of
  iterations and the path the loop takes vary across runs. This is
  the intrinsic property of the quadrant, not a tooling limitation.
  Plan for it.
- **Required commitments.** ADR-0008 named operator + ADR-0009 named
  gap-bearer. ADR-0005 approval gate on behavior-modifying writes;
  gating external publication of each constructed answer is a
  deployment choice beyond the ADR floor (some deployments treat
  each answer as itself an approval-gated artifact; others treat the
  deployment envelope as approval-gated and let answers stream).

## (Skill-design gradient case) Same job, different phase, different shape

*Published 2026-05-02. Source: 2026-05-02 essay (essay 7).*

- **Work.** Distill recurring patterns from a body of natural-language
  or structured-text artifacts into reusable, fixed units (rules,
  templates, embeddings, classification schemes). The same job
  recurs at different points in the lifecycle — once during
  exploratory authoring, once after the artifact set has settled.
- **The same job, two phases, two shapes.** The job's
  position on the
  [skill-design gradient](../glossary.md#skill-design-gradient)
  changes with phase, not with model capability. Two implementations
  of the same job coexist legitimately:
  - **Design phase form.** A natural-language skill that an LLM
    invokes at runtime. The target codebase, the IDE, the relevant
    file types, and the execution environment vary across
    invocations; fixed paths and naming conventions cannot be
    assumed in advance. Sits closer to the Autonomous Agentic
    Loop Quadrant in shape (per-invocation runtime judgment), but
    is still a single skill, not a free-form ReAct loop.
    [Target identifiability](../glossary.md#target-identifiability)
    is low; [scale-resilience](../glossary.md#scale-resilience) is
    not the constraint because scale is small.
  - **Operation phase form.** Once the targets settle into a
    fixed directory layout with stable naming conventions, the
    judgment is encoded as a deterministic pipeline: embeddings +
    clustering + threshold logic, with at most a single bounded
    LLM call where unavoidable. Sits in the LLM Workflow Quadrant
    (Quadrant 3) or even the Script Quadrant (Quadrant 1) when
    the LLM call disappears entirely. Target identifiability is
    high; scale-resilience matters because the operation form
    runs over the full settled corpus rather than a per-invocation
    slice.
- **Phase placement.** The two forms sit in different lifecycle
  phases of the same project. Both can coexist; neither is a
  generalization of the other.
- **Quadrant routing for each form.**
  - Design phase form — Quadrant 4-leaning skill, but invoked
    inside a design session, so ADR-0010 (1) Phase-crossing
    decision is satisfied automatically (this *is* the design
    phase). The
    [`anti-patterns.md`](anti-patterns.md#skill-internal-phase-descent-ignored--uniform-react-or-uniform-freeze)
    "Skill-internal phase descent ignored" entry guards against
    forcing this form into a frozen pipeline before the targets
    have settled.
  - Operation phase form — Quadrant 3 (or Quadrant 1) component
    in the default operation composition. No Phase-crossing
    decision is needed because the form is no longer Quadrant 4.
- **Why capability is not the lever.** A higher-capability LLM
  does not give the design-phase form fixed targets, and does
  not give the operation-phase form runtime tolerance for novel
  unseen variations. AKC's "capability ↑ → holistic judgment OK"
  principle does not cover either of the two secondary forces.
  Phase is what decides which form is structurally honest.
- **Recursion inside a single skill.** The same gradient applies
  *inside* a single skill: a design-phase skill can have one
  subcomponent that is a frozen lookup (target identifiability is
  locally high — for example, a config file at a known path) and
  another subcomponent that is runtime LLM judgment. The
  diagnostic frame walks recursively.
- **Lineage note.** The concrete examples behind this case are in
  the 2026-05-02 essay; they are not named in this navigator
  because the navigator is harness-neutral. The essay walks two
  AAP-author skills that exhibit the design-phase / operation-phase
  split for the same job.

## A note on what these cases are not

The case studies above are *illustrative routings*, not endorsed
reference architectures. The repository does not maintain a
"reference architecture" file because reference architectures
dissolve faster than the routing judgment behind them. Once the
quadrant is identified, the architecture follows from the quadrant's
properties (post-hoc separability, named gap-bearer commitment) and
the applicable ADRs — not from a template.
