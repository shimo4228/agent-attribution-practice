# Business AI Quadrants — Adoption Navigator

> **What this directory is.** A practical navigator for routing a
> piece of work to the architecture that preserves accountability
> distribution. Together with the nine ADRs, the four quadrants form
> the **two-axis structure** of this repository: the ADRs answer
> per-question (*what should be constrained, who is responsible*);
> the quadrants route the work to where those answers apply.

The four quadrants were introduced in the 2026-04-29 essay and given
their accountability-attribution treatment in the 2026-04-30 essay.
The naming used in this repository (Algorithmic Search, Autonomous
Agentic Loop) replaces the source essays' provisional names
(Classical AI Quadrant, ReAct Quadrant) with harness-neutral labels;
the source names are preserved as aliases in [`../glossary.md`](../glossary.md).

## The two axes

| | Pre-defined workflow | Exploratory |
|---|---|---|
| **Deterministic** | (1) Script Quadrant | (2) Algorithmic Search Quadrant |
| **Semantic-judgment** | (3) LLM Workflow Quadrant | (4) Autonomous Agentic Loop Quadrant |

- **Horizontal axis** — Can the work be expressed as deterministic
  rules, or does it require semantic judgment that an LLM provides?
- **Vertical axis** — Is the *control flow* of the work pre-defined
  (the next step is decided by code written in advance), or is it
  exploratory (the next step must be decided at runtime, in light of
  what was just observed)?

The vertical axis maps directly to Anthropic's "Building Effective
Agents" distinction between *predetermined code paths* and *LLM
dynamically directs its own process*.

## The four quadrants

### (1) Script Quadrant

Deterministic × pre-defined. Scripts and pipelines without LLMs.
Form filling, data normalization, lookup, validation. AAP's nine ADRs
are largely out of scope here — classical software engineering
applies.

### (2) Algorithmic Search Quadrant

Deterministic × exploratory. Classical search, dynamic programming,
MCTS, reinforcement learning. Routing optimization, scheduling,
combinatorial allocation. Out of scope for this repository's
LLM-focused ADRs; the concerns are model validation, search-space
correctness, and standard SE accountability.

### (3) LLM Workflow Quadrant

Semantic-judgment × pre-defined. Deterministic control flow plus
bounded LLM calls with named, documented roles and explicit
input/output schemas. Each LLM call's contribution is **post-hoc
separable**; redirect succeeds. Continuous with the workflow patterns
documented in Anthropic's "Building Effective Agents" — prompt
chaining, routing, parallelization, orchestrator-workers,
evaluator-optimizer — and OpenAI's "A Practical Guide to Building
Agents."

The default for most current LLM applications. The 2026-04-30 essay
argues that this quadrant has been chronically routed to (4) by
elimination because the industry vocabulary lacks a positive name for
it.

### (4) Autonomous Agentic Loop Quadrant

Semantic-judgment × exploratory. The LLM decides each next step at
runtime — the "ReAct" pattern (Yao et al. 2022) and its descendants
(Deep Research, exploratory coding agents, open-ended browsing).
Legitimate when the work genuinely requires open-ended exploration;
the path is not predictable in advance, and bounding the LLM call's
role would prevent the work from being done.

Choosing this quadrant commits the deploying organization to a
**non-removable attribution gap**: when judgment elements blend at
runtime, post-hoc separability is foreclosed, and redirect cannot
succeed at the level of separable contributions. See ADR-0009
[Triage Before Autonomy](../adr/0009-triage-before-autonomy.md) and
[`../glossary.md#attribution-gap`](../glossary.md#attribution-gap).

## How to use this directory

Read in order:

1. [`decision-tree.md`](decision-tree.md) — a four-question triage
   for routing a piece of work to one of the four quadrants and the
   ADR set that applies.
2. [`governance-mapping.md`](governance-mapping.md) — a dense table
   mapping each quadrant to its applicable ADRs, required controls,
   and redirect path.
3. [`case-studies.md`](case-studies.md) — concrete examples (FAQ
   classification, research assistant, scheduling, contract review,
   invoice matching, customer support chat) routed to quadrants and
   architectures.
4. [`anti-patterns.md`](anti-patterns.md) — common ways the quadrant
   choice goes wrong, why each fails, and how to recover.

## Two-axis structure — ADRs and Quadrants

The two artifacts answer different questions:

- **The nine ADRs** answer "what should be constrained, and who is
  responsible" *given that we are operating in a quadrant where
  accountability distribution is meaningful.*
- **The four quadrants** answer "is accountability distribution
  meaningful for this work, and if so, in which architectural
  regime?"

The two-axis posture preserves AAP's "*nine judgments, not a fixed
framework*" stance: the quadrants are a diagnostic frame for routing,
not a prescriptive taxonomy. Different quadrants apply different
*subsets* of the ADR set with different load-bearing weight; the ADR
set itself is not partitioned by quadrant.

## Status

This layer is part of AAP's active publication and will be updated as
adoption experience accumulates. The vocabulary (especially the
boundary between LLM Workflow Quadrant and Autonomous Agentic Loop
Quadrant for multi-turn chat agents) is still being refined; see
ADR-0009's open questions.
