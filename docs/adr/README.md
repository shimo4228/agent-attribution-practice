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
| [0009](0009-triage-before-autonomy.md) | Triage Before Autonomy (experimental) | experimental |
| [0010](0010-phase-separation.md) | Phase Separation Between Design and Operation (experimental) | experimental |

ADR-0001, 0002, and 0003 form a **prohibition-strength hierarchy**: absence (strongest) → scaffolding enforcement → untrusted boundary (probabilistic, weakest). Walk the hierarchy top-down when designing a prohibition.

ADRs 0009 and 0010 form a **triage pair**. ADR-0009 sits *before* the other ADRs in the order of application: it triages whether the work is in the Autonomous Agentic Loop Quadrant (where all ten ADRs are load-bearing), the LLM Workflow Quadrant (where 0001–0007 apply), or out of scope (Script / Algorithmic Search Quadrants). ADR-0010 surfaces the *Phase-crossing decision* when an Autonomous Agentic Loop Quadrant component is placed in the operation phase of a deployment. Phase (design vs operation) is independent of Quadrant — every Quadrant can appear in either phase. See [`../quadrants/`](../quadrants/) for the navigator. Together, the ten ADRs and the four Business AI Quadrants form the repository's **two-axis structure**.

## Format

Each ADR follows: Status / Date / Context / Decision / Alternatives Considered / Consequences. The intent is that the *decision* — what should be constrained, and who is responsible — persists across implementation changes.

See [../thesis.md](../thesis.md) for the underlying thesis (*accountability distribution*, not capability distribution), [../quadrants/](../quadrants/) for the adoption navigator, and [../manifesto.md](../manifesto.md) for the open questions this work does not attempt to answer.
