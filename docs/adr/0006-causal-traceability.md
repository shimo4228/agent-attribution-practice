# ADR-0006: Causal Traceability

> **Summary.** After an incident, you must be able to reconstruct *which version of the agent did what, in response to what input, producing what output, approved by whom*. This is not achievable at runtime — it has to be designed into the artifact layer at build time. Append-only logs, versioned behavior-modifying artifacts, and the audit trail from the approval gate are the minimum substrate.

## Status
accepted

## Date
2026-04-13 (first articulated) / 2026-04-19 (ADR-ized in this repository)

## Context

A typical production incident with an LLM-based agent ends with someone
writing an incident report. The "root cause" field is where the shape of the
system becomes visible.

Conventional agents fail this step: the final LLM output and the list of
external API calls are logged, but *why* the agent decided that — which
prompt fragment was load-bearing, which version of identity or rules was
active, which distilled knowledge flowed into the context — is unrecoverable.
What remains is an output string and an unstructured conversation log.

Organizations have solved the analogous problem for human decisions over
centuries: PR review, CAB, audit trails, version control, postmortem
process. Each produces a *chain* you can walk backwards from the incident
to the decision. Agents today are often deployed without the equivalent.

This principle names the requirement: every behavior-modifying event must
be recoverable after the fact, through artifacts that already exist at
build time.

## Decision

The agent's artifact layer must support *post-incident causal
reconstruction* as a first-class requirement. Concretely:

### What must be recoverable

For any incident, the operator should be able to answer:

1. **Which inputs reached the agent?** Complete episode record, verbatim.
2. **Which version of the agent's behavior was active?** Identity, rules,
   constitution, and skills pinned to a specific version at the time.
3. **Which approvals gated the active version?** Audit record linking the
   version to a human approver (or rejecter) and a timestamp.
4. **Which external surface was touched?** One adapter (ADR-0004), one
   destination, recorded per event.
5. **What did the agent output?** Final output stored with the input that
   produced it.

### Required artifacts

| Artifact | Property |
|----------|----------|
| Episode log | Append-only, one line per event, atomic writes |
| Identity / rules / constitution | Version-pinned; writes go through ADR-0005 approval gate |
| Audit trail | Every approval decision (accept, reject, manual CRUD) with reason |
| Architecture Decision Records | Why the current structure exists; updated as the system evolves |
| Adapter call log | Request/response pairs scoped to the single pinned adapter |

### Append-only is structural, not advisory

Rotating, truncating, or deleting the episode log destroys the only full
record of what the agent actually saw. The log must be append-only as a
system property (umask, write-once discipline, backup before truncate
if ever necessary). Storage growth is a cost to be paid, not an excuse
to delete.

### Versions, not snapshots

Behavior-modifying artifacts are versioned so that "which rules were
active when incident X happened" has a definite answer. A mutable file
that gets overwritten in place breaks causal traceability even if all
the other pieces are intact.

## Alternatives Considered

- **Runtime observability only** (metrics, tracing, alerts) — Necessary
  but insufficient. Runtime data captures *that* something happened;
  causal traceability needs the *why*, which depends on the versioned
  artifact state at the time.
- **Replay the incident in a development environment** — Tempting
  but unsound for probabilistic generation. Replay produces different
  output. What you need is a record of the actual decision, not a
  reconstruction.
- **Centralized logging service** (ELK, Splunk, etc.) — Useful
  infrastructure, but orthogonal to this decision. The decision is
  *what* to log and with what structure, not where to store it.
- **Trust the LLM's self-explanation** — Rejected. A probabilistic
  model's post-hoc rationalization is a narrative, not a cause.

## Consequences

- Incidents become analyzable. "Why did the agent do X?" has a
  procedure, not a speculation.
- Accountability is distributable. The incident chain identifies which
  human approved which version, which code introduced which capability
  (or its absence). Blame does not default to the on-call engineer by
  elimination.
- Third parties (auditors, regulators, organizational reviewers) can
  inspect the chain without running the agent. This is a precondition
  for adoption in high-stakes environments.
- Storage costs grow. Append-only plus version-pinning means the
  system accumulates history indefinitely unless archive tiers are
  added. This is deliberate.
- Development style shifts. Designing for "what would the postmortem
  look like" up front is cheaper than retrofitting traceability after
  the first incident.

### Convergence with older disciplines

The required pieces are structurally identical to what organizations
use for human decisions:

| Organizational practice | Traceability equivalent |
|-------------------------|-------------------------|
| Segregation of duties | Single External Adapter (ADR-0004) |
| Four-eyes principle | Human Approval Gate (ADR-0005) |
| Least privilege | Security by Absence (ADR-0001) |
| Internal controls | Pre-write approval audit |
| Change Advisory Board | Versioned behavior-modifying artifacts |
| Audit log | Append-only episode log |

This convergence is not decorative. The decision cost asymmetry that
makes humans adopt these practices (an incident costs orders of magnitude
more than the upstream investment) applies identically to agents.

---

**Lineage.** The thesis was written up in [事故のあとで因果を辿れるか — contemplative-agent journey article](https://zenn.dev/shimo4228/articles/agent-causal-traceability-org-adoption) (2026-04-13), building on the accountability-wall argument (2026-04-06) and preceding the blackbox two-layer argument (2026-04-14). The three articles together form the narrative spine of this repository. See [`../inspiration.md`](../inspiration.md).
