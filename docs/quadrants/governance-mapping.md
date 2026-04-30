Language: English | [日本語](governance-mapping.ja.md)

# Governance Mapping — Quadrants × ADRs × Controls

> **Purpose.** Dense reference of which ADRs apply to each quadrant,
> what controls are required, and how redirect operates when something
> goes wrong. Use this alongside [`decision-tree.md`](decision-tree.md)
> after a quadrant has been identified.

## At a glance

| Quadrant | Governance level | Applicable ADRs | Required controls | Redirect target on failure |
|---|---|---|---|---|
| (1) Script | Light (out of scope for AAP) | — | code review, tests | code author / pipeline owner |
| (2) Algorithmic Search | Light (out of scope for AAP) | — | algorithm validation, search-space audit | algorithm author |
| (3) LLM Workflow | Medium | 0001, 0003, 0004, 0005, 0006, 0007 | scaffolding visibility, audit log, approval on behavior change, schema-conformant calls | role owner of the failing LLM call → routing-logic designer → upstream-data owner |
| (4) Autonomous Agentic Loop | High | **all ten ADRs** | + pre-named gap-bearer (0008 + 0009), human approval on every external action (0005), full causal trace (0006), one accountable operator (0008), **operation-phase placement requires a recorded Phase-crossing decision (0010)** | the pre-named gap-bearer (principled gap; redirect at component level is foreclosed) |

### Phase note (orthogonal to Quadrant)

Phase (design vs operation) is independent of Quadrant — see
[README — Phase is a third dimension](README.md#phase-is-a-third-dimension-not-a-partition)
for the framing. The governance-specific consequence: **placing
Quadrant 4 in the operation phase invokes ADR-0010**, adding the
Phase-crossing decision (recorded at deployment time) to the
controls listed above for Quadrant 4. Design-phase Quadrant 4
placements satisfy this automatically.

The empirical default for operation-phase workflows is a composition
of Quadrant 1 + 3 (+ 2 where applicable). Deviations are surfaced
through the [`decision tree`](decision-tree.md) Q0 + Q4 path.

## Per-quadrant detail

### (1) Script Quadrant — out of scope

LLM-related ADRs do not apply. Failure analysis routes to the code
author or pipeline owner through normal SE channels.

### (2) Algorithmic Search Quadrant — out of scope

Out of scope for this repository's LLM-focused ADRs. Failure routes
to the algorithm author or the upstream specification owner.

### (3) LLM Workflow Quadrant — Medium governance

See [README — LLM Workflow Quadrant](README.md#3-llm-workflow-quadrant)
for the operational signal. Six ADRs apply:

| ADR | What it requires for this quadrant |
|---|---|
| [0001 Security by Absence](../adr/0001-security-by-absence.md) | The harness does not ship code that executes shell commands or makes arbitrary outbound HTTP. Each LLM call is invoked through a narrow function with a documented schema. |
| [0003 Untrusted Content Boundary](../adr/0003-untrusted-content-boundary.md) | RAG content, accumulated state, and any content fetched from external sources is treated as untrusted when injected into prompts. |
| [0004 Single External Adapter](../adr/0004-single-external-adapter.md) | The workflow as a deployed unit has at most one external adapter. Multi-surface workflows split into per-surface deployments. |
| [0005 Human Approval Gate](../adr/0005-human-approval-gate.md) | Changes to the workflow's prompts, role definitions, schemas, or routing logic go through a human approval gate. Runtime behavior is not gated; the workflow itself is. |
| [0006 Causal Traceability](../adr/0006-causal-traceability.md) | Each LLM call's input, output, role identifier, and timestamp are logged. The workflow's control flow is reconstructible from logs. |
| [0007 Scaffolding Visibility](../adr/0007-scaffolding-visibility.md) | Prompts, role definitions, schemas, routing rules, and the workflow's control flow are version-controlled files. |

ADR-0008 (One Agent, One Human) is not strictly required in this
quadrant — the workflow can be operated by a team if the audit chain
is preserved — but if the workflow has external side effects, naming
an accountable operator follows the same logic and is recommended.

ADR-0002 (Deterministic Prohibition at the Scaffolding Layer) and
ADR-0009 (Triage Before Autonomy) are not in the table above because
they apply *across* quadrants rather than within: 0002 is the
fallback when 0001 cannot achieve absence; 0009 is the triage that
selected this quadrant in the first place.

#### Redirect path for LLM Workflow Quadrant

When the workflow produces a wrong output, the operator walks the
audit log to the failing component:

1. **Which LLM call returned a wrong result?** → role owner of that
   call (model selection, prompt design, schema specification).
2. **Was the wrong call invoked due to a routing error?** →
   routing-logic designer.
3. **Was the call's input wrong?** → upstream-data owner (data
   ingestion, RAG index curator, prior-step role owner).

Each step has a single owner. Distribution succeeds.

### (4) Autonomous Agentic Loop Quadrant — High governance

See [README — Autonomous Agentic Loop Quadrant](README.md#4-autonomous-agentic-loop-quadrant)
for the operational signal and the attribution-gap commitment. All
ten ADRs apply. ADRs 0005, 0006, 0008, 0009 carry the most weight
because they are what makes the named gap-bearer's commitment
operational. ADR-0010 adds the Phase-crossing decision when the
placement is operation-phase.

| ADR | What it requires for this quadrant |
|---|---|
| [0001 Security by Absence](../adr/0001-security-by-absence.md) | The agent does not ship capabilities it should not have. Especially load-bearing here because runtime tool selection means any capability can be invoked. |
| [0002 Deterministic Prohibition at Scaffolding](../adr/0002-deterministic-prohibition-at-scaffolding.md) | Capabilities that cannot be made absent are gated by PreToolUse hooks or structural quarantine, *outside* the LLM. |
| [0003 Untrusted Content Boundary](../adr/0003-untrusted-content-boundary.md) | Episode logs, accumulated memory, and self-authored distillation are all treated as untrusted when read back into the loop. |
| [0004 Single External Adapter](../adr/0004-single-external-adapter.md) | One agent process = one external adapter. Multi-surface deployments require splitting agents. |
| [0005 Human Approval Gate](../adr/0005-human-approval-gate.md) | **Load-bearing.** Every behavior-modifying write goes through a named human. Every external action passes a human approval gate; the alternative is unbounded autonomous side effects. |
| [0006 Causal Traceability](../adr/0006-causal-traceability.md) | **Load-bearing.** The loop's iterations, tool calls, observations, and prompts are logged in append-only form. Causal trace must enable post-incident reconstruction even though component-level redirect is foreclosed. |
| [0007 Scaffolding Visibility](../adr/0007-scaffolding-visibility.md) | The loop's prompt template, available tools, system prompt, and termination criteria are version-controlled files. |
| [0008 One Agent, One Human](../adr/0008-one-agent-one-human.md) | **Load-bearing.** Exactly one named human is bound to the agent process. Rotation is compatible if formal. |
| [0009 Triage Before Autonomy](../adr/0009-triage-before-autonomy.md) | **Load-bearing.** The triage decision and the named gap-bearer are recorded at deployment time. Defaulting into this quadrant or naming the gap-bearer post-incident are forbidden. |
| [0010 Phase Separation](../adr/0010-phase-separation.md) | **Load-bearing for operation-phase placements.** A Phase-crossing decision is recorded at deployment time: when a new pattern surfaces in operation, will the autonomous loop handle it dynamically in place, or route it back to design as feedback? Both answers admissible; "we'll figure it out" is not. Design-phase placements (coding agents, Deep Research) satisfy this automatically. |

#### Redirect path for Autonomous Agentic Loop Quadrant

Component-level redirect does not succeed in this quadrant. The
*principled redirect impossibility* is intrinsic: model output, tool
selection, history reference, and prompt context blend at runtime,
foreclosing post-hoc separability.

Redirect therefore operates at a different level:

1. **System-level failure** is identified through ADR-0006 causal
   traceability — the trace shows *that* the loop failed, even
   though it cannot show *which judgment element* contributed how
   much.
2. **Responsibility lands on the pre-named gap-bearer** (ADR-0008 +
   ADR-0009). The gap-bearer is the operator who absorbed the
   attribution gap as a pre-deployment commitment.
3. **The gap-bearer decides the post-incident action** — retrain,
   re-architect (move work to the LLM Workflow Quadrant), retire
   the deployment. Post-incident analysis cannot identify a single
   responsible component, so post-incident actions operate at the
   architectural level, not the component level.

This is a degraded form of redirect compared to (3) — it terminates
at one party rather than distributing — but it is *redirect*, not
the *moral crumple zone* failure where responsibility lands on
whoever happens to be operationally proximate without having
committed to bear it.

## Cross-quadrant note: prohibition-strength hierarchy

ADRs 0001 → 0002 → 0003 form a [prohibition-strength
hierarchy](../glossary.md#prohibition-strength-hierarchy). Quadrant-specific
consequences:

- In the LLM Workflow Quadrant, scaffolding enforcement (0002) gates
  capabilities the workflow should not invoke even by mistake.
- In the Autonomous Agentic Loop Quadrant, scaffolding enforcement
  (0002) is essential because runtime tool selection means
  probabilistic prohibition will not hold under prompt injection.
