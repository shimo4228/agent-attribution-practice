# ADR-0007: Scaffolding Visibility

> **Summary.** Every non-weights component of an agent — system prompts, personas, rules, identity, tool definitions, agent loop, harness logic — is **scaffolding**. Scaffolding must be materialized as files under version control, inspectable by the operator, and changeable only through the approval gate. Operator-level visibility is non-negotiable. External visibility is a separate question with its own tradeoffs.

## Status
accepted

## Date
2026-04-14 (articulated) / 2026-04-19 (ADR-ized in this repository)

## Context

An AI agent has two layers that shape its behavior:

- **Model weights** — what the LLM learned during pre-training and
  post-training (RLHF, Constitutional AI, instruction tuning). Changes
  here require training; contents are opaque by construction.
- **Scaffolding** — everything outside the weights that still shapes
  behavior: system prompts, personas, rules, skills, tool definitions,
  RAG indices, the agent loop, safety gates, session management, the
  runtime harness. Changes here are ordinary file edits.

The industry trend is to move scaffolding inward. Chain-of-Thought began
as an external prompt ("Let's think step by step") and has been absorbed
into reasoning models' weights. System prompts get distilled into
post-training runs. Behavioral rules become Constitutional AI clauses in
training data. The scaffolding layer shrinks; the weights layer grows.

This has two consequences:

1. **Technical opacity.** Behavior encoded in weights cannot be read by
   `grep`. It cannot be versioned, diffed, or reverted without
   retraining. A postmortem cannot point at "the line that caused this."
2. **Commercial secrecy.** The scaffolding that remains outside the
   weights is often hidden for competitive reasons. Prompt engineering
   is a differentiator; published scaffolding is scaffolding your
   competitors can copy. A 2026-03-31 accidental source-code disclosure
   at one of the most safety-conscious AI companies illustrated that
   even they had not published their scaffolding — the competitive
   environment did not allow it.

The result: both layers of what shapes agent behavior are structurally
difficult to see. The accountability-wall thesis (see ADR-0005 and the
accompanying essays) requires that behavior be causally traceable. You
cannot trace cause through a layer you cannot read.

## Decision

Every component that is *not* the model weights is scaffolding, and
scaffolding must be:

1. **Materialized as files** — identity, rules, constitution, skills,
   tool definitions, agent-loop logic, prompt templates. No component
   that shapes behavior lives only in a runtime variable, a
   proprietary API response, or a mutable in-memory cache.
2. **Version-controlled** — changes are diffable, revertible, and
   attributable to a specific commit (or equivalent record).
3. **Operator-inspectable** — anyone running the agent can read the
   full scaffolding of the instance they are running. There is no
   "private" scaffolding that the operator is trusted to deploy but
   not trusted to read.
4. **Gated at write time** — changes to behavior-modifying scaffolding
   flow through ADR-0005 (Human Approval Gate). No silent rewrites.

### Operator visibility vs external visibility

Two different visibility questions should not be conflated:

| Question | Position |
|----------|----------|
| Can the operator read the full scaffolding of the agent they run? | **Required, always.** |
| Can external reviewers / users / auditors read it? | Separate question, with legitimate commercial tradeoffs. |

This ADR takes no position on the second question. Commercial operators
may have valid reasons to redact scaffolding from public view. What this
ADR forbids is using *commercial secrecy as a reason to also hide it
from the operator* — because then no human in the loop can review
changes, and ADR-0005 and ADR-0006 become unenforceable.

### What "materialized as files" rules out

- Scaffolding baked into model weights beyond what the base model
  encoded. If RLHF adds behavioral rules that the operator cannot
  read out as files, those rules have escaped the scaffolding layer
  and become unaccountable. (This ADR does not demand training-data
  transparency. It demands that behavior *as applied to this
  deployed agent* remain in the scaffolding layer.)
- "Built-in" safety behaviors maintained only as vendor policy with
  no corresponding artifact the operator can inspect.
- Rules expressed only as runtime variables initialized from a
  proprietary SDK call.

### What it does not rule out

- Using a closed-weights foundation model. The weights are opaque by
  construction; this ADR concerns the scaffolding around the weights,
  not the weights themselves.
- Redacting scaffolding from public publication. Operator visibility
  is the requirement; public visibility is a separate choice.
- Performance optimizations (caching, compilation) that mirror the
  file-level scaffolding in a runtime form. The file is canonical.

### Minimum set for causal tracing

The resolution to "visible vs hidden" is not "publish everything" or
"hide everything." It is defining the **minimum set** that makes causal
tracing possible. At least:

- Which scaffolding version was active at the time of the incident
- Which inputs reached the agent (ADR-0006)
- Which approval record gated the active scaffolding version (ADR-0005)

Organizations may disclose this minimum set without disclosing the
scaffolding's full text. The decision about where exactly to draw the
disclosure line is operator-specific. Below the line, there is no
accountability chain at all.

## Alternatives Considered

- **Accept that modern agents are partly weights-internalized; measure
  behavior externally** — Rejected as an operator-layer principle.
  External behavioral testing is useful for evaluation, insufficient
  for postmortem causal tracing. A test tells you *that* the agent
  behaves a certain way, not *why* it did so on a specific input.
- **Scaffolding is a commercial asset; operator visibility is
  optional** — Rejected. If the operator cannot read the
  scaffolding, human approval (ADR-0005) and causal traceability
  (ADR-0006) cannot be enforced. Optional visibility is not
  visibility.
- **Require public disclosure of all scaffolding** — Rejected as
  overreach. Operator visibility is the load-bearing requirement;
  public visibility is a separate dimension with legitimate
  tradeoffs.
- **Trust vendor attestations of scaffolding content** — Rejected.
  Attestation without inspectable artifact is the signpost pattern
  (see ADR-0005 context).

## Consequences

- Building an agent on top of a vendor that refuses to expose
  scaffolding requires either accepting an unaccountable layer
  (violating ADR-0006) or choosing a different vendor.
- Small operators and individual developers — who have no
  commercial reason to hide scaffolding — naturally satisfy this
  ADR by default. The existence proof comes from that side.
- The line between "healthy scaffold dissolution" (scaffolding
  that melts into clear conversational patterns / easily-read
  files) and "unhealthy internalization" (scaffolding absorbed
  into weights where it cannot be read back) is where this ADR
  draws its boundary. See [`../glossary.md`](../glossary.md).
- Postmortems become possible. The "which scaffolding version was
  active" question has an answer.

---

**Lineage.** The analysis was developed in [AIエージェントのブラックボックスは二層ある](https://zenn.dev/shimo4228/articles/agent-blackbox-capitalism-timescale) (2026-04-14), the third of the three articles that form the spine of this repository. Earlier articulations of the scaffolding/weights distinction and operator-side file-materialization appeared in contemplative-agent's design evolution (see the series entries in [`../inspiration.md`](../inspiration.md)).
