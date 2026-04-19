# Glossary

Key terms used across the eight ADRs and the accompanying essays. Where
a term is load-bearing (carries argumentative weight, not just naming
convenience), an example is included.

## Accountability distribution

The pattern that organizations developed over centuries — PR review,
audit logs, segregation of duties, four-eyes approval, change advisory
boards, postmortems — all of which distribute *responsibility* across a
chain rather than concentrating it in a single actor. The thesis of this
repository: accountability distribution applies to agents too, and
produces roughly the same structural shapes.

Contrast with: *capability distribution*, which is the current dominant
design axis (what the agent *can do*) and which does not produce an
accountability chain.

## Scaffolding

All the non-weights components that shape an agent's behavior: system
prompts, rules, identity, tool definitions, RAG indices, agent loop
logic, runtime harness, safety gates, session management. The term is
used in AI safety research (see Davidson et al., 2023) to distinguish
externally-constructible behavior-shaping from behavior baked into
model weights.

In this repository, scaffolding is contrasted with weights along two
axes: *inspectability* (scaffolding is text you can read; weights are
not) and *locus of change* (scaffolding changes by editing files;
weights change by retraining).

See ADR-0007 for the position this repository takes on the distinction.

## Scaffold dissolution (two senses — do not conflate)

The word *dissolution* appears with opposite valences in adjacent
discourse. Keeping them distinct is important.

### Healthy dissolution — into human cognitive patterns

A pattern first articulated in the Agent Knowledge Cycle (AKC): as
operators and agents internalize a skill through repeated use, the
explicit skill definition becomes unnecessary — the pattern runs
naturally in conversation, in reviewer intuition, in how problems get
framed. The skill file can eventually be simplified or removed. This
dissolution preserves what matters (the judgment persists in human
practice; nothing about the system becomes less inspectable).

### Unhealthy internalization — into model weights

A pattern critiqued in the 2026-04-14 blackbox essay: explicit
behavioral rules get absorbed into LLM weights via training (RLHF,
Constitutional AI, instruction tuning). The rule still "runs" — but
it runs probabilistically, it cannot be inspected by `grep`, it cannot
be diffed or reverted without retraining. This dissolution removes
exactly what accountability distribution requires.

The words are the same. The direction of dissolution is opposite.
One dissolves into transparent human practice; the other dissolves
into opaque statistical behavior. This repository critiques the
latter and is continuous with the former.

## Security by Absence

Dangerous capabilities are not restricted — they are never implemented.
The full principle is in ADR-0001. The short test: if the answer to
"does capability X exist?" is not answerable by `grep`, the system does
not have Security by Absence.

## Deterministic prohibition at the scaffolding layer

When a capability cannot be made absent, the prohibition is enforced by
scaffolding (PreToolUse hooks, structural quarantine, adapter gates) —
outside the LLM, firing on 100% of matching inputs. Contrasted with
probabilistic prohibition in model weights (prompt clauses, convention
files, constitutional instructions), which comply ~50-80% of the time
under normal conditions and less under adversarial pressure. See ADR-0002.

## Prohibition-strength hierarchy

Absence (ADR-0001) > scaffolding enforcement (ADR-0002) > untrusted
boundary (ADR-0003). A prohibition is designed by walking the hierarchy
top-down: drop to the next layer only when the current one genuinely
cannot hold the capability. Most current AI safety discourse treats
probabilistic constraint as if it were the only option, omitting that
the top two layers exist.

## External adapter

Any component capable of a side effect observable outside the agent's
local data directory: HTTP POSTs to third parties, monetary transactions,
messages sent to other systems, files written outside the data directory.
Not external: local LLM inference, writes inside the data directory, pure
computation. See ADR-0004 for the "at most one per process" rule.

## Untrusted content

All accumulated agent state — episode logs, knowledge store, distilled
identity, learned rules — treated as untrusted whenever read back into a
prompt, *including content the agent itself authored*. The non-obvious
part: self-authored distillation inherits the taint of the external
input it summarized. See ADR-0003.

## Approval gate

A structural checkpoint where a human sees the generated output of a
behavior-modifying command and approves or rejects before the write
occurs. Not a flag that can be disabled. See ADR-0005.

## Causal traceability

The ability to reconstruct, after an incident: which inputs reached the
agent, which version of scaffolding was active, which approvals gated
that version, which external surface was touched, what the final output
was. See ADR-0006.

## Accountable operator

The one specific, named human bound to an agent process, who approves
behavior-modifying changes and owns incident response for that agent.
Not a team, not a role, not a committee — a named individual. Rotation
is compatible if the rotation is formal and each rotation slot has
exactly one named person. See ADR-0008 (experimental).

---

## Dissolution signature checklist (provisional)

> **Status.** The following distinction between healthy and unhealthy
> dissolutions is provisional. It was first drafted through discussion,
> has not yet been validated against edge cases, and will likely be
> refined or restructured. Treated as an aid to review, not a hard rule.

A dissolution (of any kind — scaffolding into practice, rules into
convention, safety into training) preserves accountability if all of:

1. **External inspectability** — what dissolved can still be read by
   an outside reviewer, in some form.
2. **Locus of agency preserved** — the decision-making capacity
   remains with humans or with an inspectable artifact, not with a
   probabilistic process whose behavior cannot be audited.
3. **Failure visibility** — when the dissolved mechanism malfunctions,
   the malfunction is observable.
4. **Causal recoverability** — after an incident, the dissolved
   mechanism can be traced back to its origin.
5. **Reversibility** — humans retain the capacity to undo the
   dissolution if it proves harmful.

A dissolution that violates two or more of these should be treated as
unhealthy internalization rather than healthy dissolution. This
heuristic needs further testing against real cases.
