# Thesis — Accountability Distribution

> The project is named **Agent Attribution Practice (AAP)**. The name
> points to the practice; this thesis points to what the practice
> achieves. Attribution — who authored the behavior, who caused what,
> who must be able to trace it afterward — is what gets distributed.
> Accountability is the downstream shape that attribution takes when
> the practice holds.

## What this repository is

A record of recurring judgments, discovered through implementing and
operating an autonomous AI agent, about how accountability should be
distributed across an agent-equipped system.

Not a governance framework. Not a set of policies. A trail of judgments
that each took the form of an Architecture Decision Record, each grounded
in a concrete implementation, each inheriting from 300+ years of
civilizational practice about containing powerful actors.

## The core observation

An often-quoted line from the [accountability-wall essay](https://zenn.dev/shimo4228/articles/ai-agent-accountability-wall):

> Implementation dissolves; judgment persists. Hooks will be replaced by
> some other mechanism. Signposts and physical barriers are temporary
> forms. What remains is the layer of judgment — **what should be
> constrained, and who is responsible.**

And its structural sibling:

> What organizations have spent centuries refining is not the
> distribution of *capability*, but the distribution of **accountability**.

These two lines are the thesis of this repository. The eight ADRs that
follow are partial answers to "what should be constrained, and who is
responsible" — discovered through the friction of running an actual
agent in production, not deduced top-down from a governance theory.

## Why the observation matters now

The industry's dominant approach to aligning AI agent behavior is to
dissolve structural constraints into probabilistic layers: training
refusals into weights, Constitutional AI clauses into RLHF runs, system
prompts into post-training. Each move trades deterministic constraint
(grep-auditable, reversible, portable) for probabilistic behavior
(unauditable, reversible only by retraining, model-dependent).

This is not inherently wrong. Some dissolutions preserve what matters —
inspectability, reversibility, locus of human agency. Others do not. The
ADRs in this repository describe the dissolutions that preserve.

## What the eight principles answer

Each ADR answers a version of "what should be constrained, and who is
responsible":

| ADR | Constraint | Responsibility |
|-----|-----------|---------------|
| 0001 Security by Absence | Capabilities that do not exist cannot be invoked | Code review catches capability addition |
| 0002 Deterministic Prohibition at Scaffolding | When absence is unachievable, prohibit in the harness, not in weights | Hook / quarantine is version-controlled and auditable |
| 0003 Untrusted Content Boundary | Accumulated memory cannot grant authority | Operator inspects tainted files |
| 0004 Single External Adapter | Blast radius is bounded by design | Per-process accountability per adapter |
| 0005 Human Approval Gate | Behavior changes require human sign-off | Named approver in audit record |
| 0006 Causal Traceability | Every event reconstructible post-incident | Operator owns the audit chain |
| 0007 Scaffolding Visibility | Behavior lives in files, not weights | Operator can inspect what they run |
| 0008 One Agent, One Human | One named human per agent process | The endpoint of the accountability chain |

ADRs 0001, 0002, and 0003 form a **prohibition-strength hierarchy**:
absence > scaffolding enforcement > untrusted boundary. When a prohibition
cannot hold at a layer, it drops to the next. Most published AI safety
work lives at the weakest layer (probabilistic text constraints) without
acknowledging that the stronger layers exist and are available.

## What this repository does not claim

- That these eight are complete. They are what surfaced from one
  implementation. Other implementations will surface others.
- That the specific implementations (Hooks, CLI, JSONL logs) are
  durable. They are not — implementation dissolves. The *judgment*
  encoded in each ADR is what is meant to persist.
- That these principles answer the civilization-scale questions about
  AI direction, labor, or consent. Those remain open; see
  [`manifesto.md`](manifesto.md).
- That top-down policy frameworks are wrong. They are a different
  layer, with a different method. This repository is about what
  emerges from the bottom.

## Reading order

For readers new to the material:

1. This file.
2. [`adr/README.md`](adr/README.md) for the index.
3. [`adr/0001-security-by-absence.md`](adr/0001-security-by-absence.md)
   as the cleanest entry point — the audit test at the end makes the
   principle concrete in a way that applies regardless of stack.
4. The three essays linked in [`inspiration.md`](inspiration.md), in
   publication order, for the narrative that the ADRs encode.
5. [`manifesto.md`](manifesto.md) for the open questions that motivate
   the work but that it does not try to answer.
