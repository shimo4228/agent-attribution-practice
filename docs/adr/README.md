# Architecture Decision Records

Each ADR records a structural judgment about how autonomous AI agents should be built and operated — discovered through implementation and operation of `contemplative-agent`, not prescribed top-down.

## Index

| ADR | Title | Status |
|-----|-------|--------|
| [0001](0001-security-by-absence.md) | Security by Absence | accepted |
| [0002](0002-deterministic-prohibition-at-scaffolding.md) | Deterministic Prohibition at the Scaffolding Layer | accepted |
| [0003](0003-untrusted-content-boundary.md) | Untrusted Content Boundary | accepted |
| [0004](0004-single-external-adapter.md) | Single External Adapter Per Agent | accepted |
| [0005](0005-human-approval-gate.md) | Human Approval Gate for Behavior-Modifying Commands | accepted |
| [0006](0006-causal-traceability.md) | Causal Traceability | accepted |
| [0007](0007-scaffolding-visibility.md) | Scaffolding Visibility | accepted |
| [0008](0008-one-agent-one-human.md) | One Agent, One Human (experimental) | experimental |

ADR-0001, 0002, and 0003 form a **prohibition-strength hierarchy**: absence (strongest) → scaffolding enforcement → untrusted boundary (probabilistic, weakest). Walk the hierarchy top-down when designing a prohibition.

## Format

Each ADR follows: Status / Date / Context / Decision / Alternatives Considered / Consequences. The intent is that the *decision* — what should be constrained, and who is responsible — persists across implementation changes.

See [../thesis.md](../thesis.md) for the underlying thesis (*accountability distribution*, not capability distribution) and [../manifesto.md](../manifesto.md) for the open questions this work does not attempt to answer.
