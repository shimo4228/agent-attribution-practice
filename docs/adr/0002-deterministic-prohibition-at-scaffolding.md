# ADR-0002: Deterministic Prohibition at the Scaffolding Layer

> **Summary.** When a dangerous capability cannot be made absent (ADR-0001), the prohibition must still be enforced deterministically — at the scaffolding layer (hooks, quarantine, adapter gates) rather than at the model-weight layer (prompts, guidelines, constitutional clauses). Probabilistic constraint against irreversible action is decorative.

## Status
accepted

## Date
2026-04-19

## Context

ADR-0001 prefers *absence* of dangerous capabilities over *restriction* of them. Absence is the strongest form of prohibition: nothing to bypass, nothing to misconfigure, nothing to patch. Its audit test is a single `grep`.

But not every capability can be made absent. Some agents must read files, call external services, observe tool output. For those, the remaining design choice is:

1. Enforce the prohibition at the **scaffolding layer** — the harness, the hook system, the adapter — outside the LLM. The layer fires on 100% of matching inputs. The decision is derivable from configuration + input, not from model weights.
2. Enforce the prohibition at the **model-weight layer** — system prompt clauses, convention files (e.g. `CLAUDE.md`), constitutional instructions. Compliance is probabilistic (observed ~50-80% under normal conditions, lower under prompt-injection pressure).

A walled garden with a sign saying *"please do not climb"* is not the same as a walled garden. Against prompt injection, adversarial input, and the agent's own curiosity under context pressure, the second kind of wall is not a wall.

This pattern has been observed twice in operation:

- A coding agent with `Read` / `Bash` / `Grep` access to raw accumulated-state files (e.g. episode logs) can be redirected by a single injected instruction to ingest untrusted content. A convention saying *"do not read the logs"* is a probabilistic control. A PreToolUse hook that blocks the path is a deterministic control. The same agent harness can host either, but only the hook fires reliably.
- An agent that records `source_type` on stored knowledge and weights retrieval by it can look like it has a defense against memory-injection-class attacks (MINJA; Dong et al. 2025, [arXiv:2503.03704](https://arxiv.org/abs/2503.03704)). In a production audit, the soft trust weighting turned out to be *armed but neutered* — production data was dominated by the migration default and the weighting never discriminated. What actually defended the agent was a structural quarantine at the summarization boundary: external content never entered the LLM prompt at all. The effective defense was deterministic. The probabilistic layer had done no work.

The lesson in both cases is the same: *probabilistic prohibition looks like defense but is not.*

## Decision

When a prohibition cannot be achieved via absence (ADR-0001), it must be enforced at the **scaffolding layer**, not at the model-weight layer. The scaffolding layer intercepts the request before the capability fires and returns a block decision with a reason.

### Three implementation patterns

| Pattern | What it blocks | Failure mode |
|---|---|---|
| **Pre-tool hook** | A specific tool invocation matching a path/argument pattern | Novel read path the matcher does not cover; bypass via uncovered subprocess |
| **Structural quarantine** | Content entering the LLM prompt at all (e.g. summarize-then-discard) | Producer bug that lets raw content leak into the summary |
| **Adapter gate** | Outbound side effects at the single external adapter (ADR-0004) | Adapter logic accepting an attacker-influenced destination or payload |

All three share one property: **the decision does not consult the LLM**. Correctness is verifiable by reading configuration + the gating code.

### The prohibition-strength hierarchy

The three ADRs on prohibition form a strength hierarchy:

| Layer | ADR | Mechanism | Primary failure mode |
|---|---|---|---|
| Absence | ADR-0001 | Capability is never implemented | A later patch adds the capability |
| Scaffolding enforcement | ADR-0002 (this) | Capability exists but is gated by a non-LLM check | Matcher gap, misconfiguration, novel bypass path |
| Untrusted boundary | ADR-0003 | Content reaches the LLM, wrapped / annotated as untrusted | LLM compliance is probabilistic |

Walk the hierarchy from the top when designing a prohibition. Drop to the next layer only when the current one genuinely cannot hold the capability.

### What this does NOT claim

- **Not equivalent to absence.** A hook can be disabled, misconfigured, or bypassed by a path its matcher does not cover. Absence has no equivalent failure mode.
- **Not a replacement for values.** For continuous judgments — tone, care, bias detection, contextual appropriateness — the model-weight layer is the only available layer. This ADR is about *categorical prohibition*, not about values.
- **Not a license to allow.** Choosing scaffolding enforcement over absence is a concession, not a default. The question *"could this capability have been made absent instead?"* remains live for every gated capability.

### Applying the principle

Before writing a rule saying *"the agent must not do X"* as a convention, prompt clause, or constitutional instruction:

1. Can X be made absent (ADR-0001)? If yes, do that.
2. If not, can X be intercepted at the scaffolding layer? If yes, do that, and document the matcher, the bypass surface, and the residual risk.
3. If neither is achievable, the prohibition is probabilistic. Treat it as values guidance, not as a guarantee. Do not claim it is a control.

## Alternatives Considered

- **Convention file only** (e.g. *"don't read the logs"* in `CLAUDE.md`). Observed to fail under adversarial conditions and under normal operational pressure (debugging, curiosity). Useful as documentation, not as enforcement.
- **System prompt / constitutional clause.** Subject to prompt injection, context compression dropping the clause, and the model's context-dependent compliance. Probabilistic by construction.
- **Trust-weighting retrieval** (*"deprioritize the dangerous source"*). Looks like a defense on the schema; in production, the discriminating signal is usually swamped by migration defaults or missing producers. Becomes decorative.
- **Allow and audit** — permit the action, detect after the fact. Appropriate for reversible actions with low blast radius. Unacceptable for irreversible ones.

## Consequences

- A new category of artifact enters the project: **scaffolding-layer enforcement scripts and quarantine boundaries**. They are version-controlled, auditable, and testable independently of the model.
- Residual risks become **enumerable**: for every hook, the matcher's coverage gaps and known bypass paths are writable down. This contrasts with probabilistic constraints, whose residual risk can only be described as *"the model might not comply,"* which is not a risk register — it is an admission of no control.
- A violation becomes **traceable**: *did the hook fire? if no, why did the matcher miss? if yes, how was it bypassed?* — versus *"the model did not follow instructions,"* which has no diagnosable cause.
- Convention-as-security is **downgraded**. Convention files and prompt-level instructions remain useful for values and for documentation, but are no longer relied on for prohibition.
- Every new capability the agent is granted triggers the same question: *where is the scaffolding gate for the prohibition around this capability?* A capability added without a gate is a capability added without a prohibition.

---

**Lineage.** The PreToolUse hook pattern is operational in [contemplative-agent](https://github.com/shimo4228/contemplative-agent), which ships three hooks (`block-episode-logs-{read,bash,grep}.sh`) blocking coding-agent access to raw JSONL episode logs; the accompanying `docs/security/2026-04-01-episode-log-access-control.md` documents the upgrade from convention-file rule (probabilistic) to deterministic hooks. The quarantine-boundary pattern is the `summarize_record` function in the same project — the actual primary defense against MINJA-class memory injection, as established by the ADR-0021 implementation audit (contemplative-agent `docs/evidence/adr-0021/implementation-audit-20260418.md`, ADR-0028, ADR-0029). This ADR extracts the general principle from those findings and was first formalized in this repository in 2026-04-19.
