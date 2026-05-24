Language: English | [日本語](thesis.ja.md)

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

An often-quoted line from the [accountability-wall essay](https://github.com/shimo4228/zenn-content/blob/main/articles-en/ai-agent-accountability-wall-en.md):

> Implementation dissolves; judgment persists. Hooks will be replaced by
> some other mechanism. Signposts and physical barriers are temporary
> forms. What remains is the layer of judgment — **what should be
> constrained, and who is responsible.**

And its structural sibling:

> What organizations have spent centuries refining is not the
> distribution of *capability*, but the distribution of **accountability**.

These two lines are the thesis of this repository. The ten ADRs that
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

## What the ten principles answer

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
| 0009 Triage Before Autonomy *(experimental)* | Adopting an autonomous-loop architecture commits the system to a non-removable attribution gap | The triage decision and the named gap-bearer are recorded at deployment time |
| 0010 Phase Separation *(experimental)* | Operation-phase placements of the Autonomous Agentic Loop Quadrant carry costs ADR-0009's design-time commitment alone does not cover | A Phase-crossing decision is recorded explicitly at deployment time |

ADRs 0001, 0002, and 0003 form a
[**prohibition-strength hierarchy**](glossary.md#prohibition-strength-hierarchy):
absence > scaffolding enforcement > untrusted boundary. Most published
AI safety work lives at the weakest layer (probabilistic text
constraints) without acknowledging that the stronger layers exist
and are available.

Distribution requires a subject who can be redirected to. When the
architecture is chosen so that judgments blend at runtime — the
intrinsic feature of the **Autonomous Agentic Loop Quadrant** — the
redirection target dissolves; distribution then becomes the *prior*
question of whether the autonomy is required at all. ADR-0009
formalizes that prior triage and pairs it with the four
**Business AI Quadrants** (Script, Algorithmic Search, LLM Workflow,
Autonomous Agentic Loop) that route a piece of work to the
architecture preserving accountability distribution. ADR-0010 adds
a temporal layer to the triage: when a Quadrant 4 component is
placed in the operation phase of a deployment, the Phase-crossing
decision (will new operation-phase patterns be handled in place or
routed back to design as feedback?) is recorded explicitly. Phase
and Quadrant remain independent dimensions; the rule is procedural,
not architectural. The ten ADRs plus the four quadrants form a
**two-axis structure**: the ADRs answer per-question; the quadrants
route the work to where those answers apply. See
[`quadrants/`](quadrants/) for the navigator.

## Social-consequence corollary

The judgments above are framed as internal control: what to constrain,
who answers. A companion layer reads the same judgments from the
outside — as the mechanism that decides whether the accountability an
AI externalizes flows into institutions (litigation, compensation,
regulation) or converges violently onto the most visible individual. On
that reading, accountability distribution is not only governance; it is
a violence-prevention mechanism. This is a normative extension, harder
to verify than the ADRs, and is kept separate as upper rationale in
[`social-consequence.md`](social-consequence.md).

## What this repository does not claim

- That these ten are complete. They are what surfaced from one
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
4. The seven essays linked in [`inspiration.md`](inspiration.md), in
   publication order, for the narrative that the ADRs encode.
5. [`quadrants/`](quadrants/) for the adoption navigator — decision
   tree, governance mapping, case studies, anti-patterns.
6. [`manifesto.md`](manifesto.md) for the open questions that motivate
   the work but that it does not try to answer.
7. [`social-consequence.md`](social-consequence.md) for the
   social-consequence layer — why the internal judgments matter beyond
   audit, as upper rationale rather than specification.
