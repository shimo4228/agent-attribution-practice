# ADR-0005: Human Approval Gate for Behavior-Modifying Commands

> **Summary.** Every command that modifies an agent's skills, rules, identity, or constitution must present its probabilistic output to a human and wait for explicit approval before writing. No `--auto` flag. Intermediate artifacts (append-only logs, read-only indexes) are exempt.

## Status
accepted

## Date
2026-03-26 (decided) / 2026-04-19 (re-expressed in this repository)

## Context

Self-improving agents periodically distill episode history into higher-layer
artifacts: behavioral skills, operational rules, identity text, and — at the
top — a constitution. Each of these layers directly changes how the agent
acts in future sessions.

Two common patterns fail to keep a human reliably in the loop:

1. **Preview-then-run**: the workflow is "run `--dry-run`, inspect output,
   then re-run without `--dry-run`." This breaks because LLM output is
   stochastic — the preview is not the same text as the production run. What
   the human approved is not what gets written.
2. **Advisory confirmation**: an optional flag that can be skipped. Any
   automation layer (a cron job, a supervising agent, an impatient operator)
   will eventually skip it. Human-in-the-loop is not structurally enforced.

The thesis of this project (see [`../thesis.md`](../thesis.md)) is that
*judgment about what to constrain and who is responsible* must persist
across implementation changes. Behavior-modifying writes are exactly where
that judgment applies. Without a structural gate, the decision to change the
agent's identity or rules is delegated to the LLM's probabilistic output.

## Decision

Commands that write to **behavior-modifying artifacts** must present the
generated output to a human and wait for explicit approval before writing.
No `--auto` flag is provided.

### Behavior-modifying vs intermediate

| Artifact class | Approval gate | Rationale |
|----------------|---------------|-----------|
| Skills (how the agent acts) | **Required** | Directly alters behavior |
| Rules (operational policies) | **Required** | Directly alters behavior |
| Identity (who the agent is) | **Required** | Directly alters behavior |
| Constitution / values | **Required** | Highest impact |
| Episode log (append-only) | None | Raw record, no behavioral effect |
| Knowledge store (intermediate) | None | Only influences behavior through a *subsequent* behavior-modifying command, which has its own gate |

### Required flow

```
CLI invocation
  → generation (LLM)
  → display result (stdout / UI)
  → prompt: "Apply this change? [y/N]"
  → y  → write artifact + append audit record
  → N  → discard + append audit record (reason: rejected)
```

The audit record is non-negotiable. Both approved and rejected decisions
are logged, with the reason field populated for rejections and
out-of-band changes (see below).

### No `--auto` flag

The gate cannot be disabled with a flag. If a command is invoked in a
non-interactive context (CI, launchd, a supervising agent), the correct
answer is *not* to auto-approve — it is to halt and require an operator.

The two legitimate non-interactive paths are:

1. **A supervising agent or orchestrator reads the output and decides.**
   Its decision still flows through the same audit record. "Supervising
   agent" is treated as an operator, not as a bypass.
2. **A separately-invoked flag that requires an audit reason.** For manual
   CRUD operations (e.g. `remove-skill`), a non-interactive mode is
   allowed only when a human-provided `--reason` string is attached to
   the audit entry. The operator is present; only the prompt is skipped.

### Manual CRUD is covered too

Any change to a behavior-modifying artifact — including direct filesystem
edits, file removals, or bulk renames — must route through an auditable
CLI entry point that writes the same audit record. A plain `rm` on a
skill file is an uncovered path and violates the invariant.

## Alternatives Considered

- **Optional `--auto` flag for automation contexts** — Rejected. Creates
  exactly the class of path this decision is meant to close. A
  supervising agent can read output and respond instead.
- **Single `--dry-run` flag, no approval gate** — Rejected. Preview does
  not match production output for stochastic generation; what the human
  approved is not what was written.
- **Approval gate on every command including intermediate writes** —
  Rejected. Non-behavioral intermediate artifacts (the episode log,
  scheduled knowledge distillation) run periodically and do not need
  human-by-human approval. Requiring it makes continuous operation
  impossible without actually improving safety.
- **Probabilistic model-based approval** (a second "critic" LLM that
  approves or rejects) — Rejected. This is the restriction-layer failure
  mode. A probabilistic gate fails open under prompt injection. The
  point is a *human* in the loop, structurally.

## Consequences

- Human-in-the-loop is structurally enforced: there is no flag to bypass.
- The non-reproducibility problem of probabilistic generation is resolved —
  approval is granted to the actual output that will be written, not to a
  preview.
- Every behavior change has an audit entry. Causal traceability (ADR-0006)
  has a reliable source.
- CLI tools that modify behavior cannot be used in naive CI pipelines.
  This is intentional.
- The operator owns the cognitive load of reviewing each generation.
  Combined with One External Adapter (ADR-0004), this keeps an adult in
  the room at every decision that changes what the agent does or how it
  represents itself.

---

**Lineage.** This principle was crystallized in [contemplative-agent `docs/adr/0012-human-approval-gate.md`](https://github.com/shimo4228/contemplative-agent). The 2026-04-17 note in that ADR — extending the gate to manual CRUD — is included here as part of the decision, not as a later amendment.
