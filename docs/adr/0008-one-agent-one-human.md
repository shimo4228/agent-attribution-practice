# ADR-0008: One Agent, One Human (experimental)

> **Summary.** An agent process is bound to exactly one identifiable human who is accountable for it. That human approves behavior-modifying changes (ADR-0005), owns incident response, and is the endpoint of the accountability chain. "The team" is not an agent's human; a specific named person per agent instance is.

## Status
**experimental** — the principle follows structurally from ADR-0004 (one external adapter per agent) and the accountability-wall thesis, but has not yet been tested against the failure modes of team operation. See open questions at the end.

## Date
2026-04-19 (first formalization in this repository)

## Context

The accountability-wall essay argued that for AI agents to be adoptable in
organizations, "who is responsible" must have an answer. The obvious
parallel on the technical side — segregation of duties, one process per
capability — has been formalized in ADR-0004 (Single External Adapter per
Agent). But ADR-0004 splits *capability*. It does not specify who is
accountable on the human side.

Common patterns that fail to produce an accountability endpoint:

- **"The team owns the agent."** When all five members of a team are equally
  responsible, nobody is specifically responsible. In the incident
  retrospective, accountability diffuses into organizational abstraction.
- **"The agent is autonomous."** The agent is deployed with no named human
  in its operational loop. When something goes wrong, the chain ends at
  "the model did it" or at the most recent engineer who touched the
  codebase, neither of which is accountability in any meaningful sense.
- **"AI supervises AI."** A second agent (a reviewer, a critic, a
  guardrail LLM) gates the first. Both are probabilistic systems. The
  accountability chain circles without reaching a human.

The structural observation: just as an agent process should not multiplex
external side effects (ADR-0004), an agent process should not multiplex
the human accountability endpoint. Multiplexed accountability is diffused
accountability.

## Decision (experimental)

Each agent process is bound, at deployment time, to exactly one
identifiable human who is its **accountable operator**. That person:

- **Approves behavior-modifying changes** (ADR-0005). When the approval
  prompt fires, a specific named human is the approver. Not a group
  mailbox, not a rotation, not an LLM.
- **Owns incident response** for that agent. When a postmortem is
  written, the accountable operator is the endpoint — not necessarily
  the party "at fault," but the party who answers for the agent's
  behavior.
- **Is named in the agent's operational record.** Changing the
  accountable operator is itself a behavior-modifying change and
  requires approval from both the outgoing and incoming operator.

### What this permits

- **Team operation with rotation** is compatible if the rotation is
  formal: at any given time, exactly one named person is the
  accountable operator, and handoffs are explicit and logged. This is
  the on-call pattern from SRE, applied to agents.
- **Escalation chains** are compatible. The accountable operator may
  escalate a decision to a superior, but the superior then becomes the
  accountable operator for that decision (and the handoff is logged).
- **Multiple agents per human.** One human can be the accountable
  operator for many agents. The binding is one-directional: one agent
  → one human, not one human → one agent.

### What this forbids

- **Committee approval** as the default path. If approval needs a
  committee, the committee designates one member as the accountable
  operator for each decision. Committees can inform; they cannot be
  the endpoint.
- **LLM as accountable operator.** A probabilistic system cannot be
  the endpoint of an accountability chain. The chain must terminate
  at a human.
- **Anonymous or unresolved operator.** If the agent process cannot
  name its current accountable operator at any moment, it halts at
  the next approval gate.

### Behavior on operator unavailability

If the accountable operator is unavailable (vacation, incapacitation,
departure) and no handoff has occurred, the agent halts at the next
approval gate rather than defaulting to permissive behavior. "No human
is available" is a valid answer; "so go ahead without one" is not.

## Alternatives Considered

- **No human binding; any authorized operator can approve** — Rejected
  as a default. Without a binding, approval becomes whoever happens to
  be around, and the postmortem has no definite endpoint.
- **Binding to roles, not individuals** (e.g. "the security
  engineer") — Weaker. Roles change occupants, responsibilities
  diffuse during transitions, and role-to-person resolution is
  implicit at the exact moments accountability is needed.
- **Binding to teams with collective sign-off** — Rejected as
  default. Logged collective sign-off is weaker than a named
  individual because the incident review has no clear endpoint.
  Teams can still operate via rotation as described above.
- **Binding an agent to another agent (AI supervisor)** — Rejected.
  The accountability chain must terminate at a human. A second agent
  can assist in the review; it cannot *be* the accountable operator.

## Open questions (why this is experimental)

1. **Scaling to enterprise.** Most enterprise deployments assume team
   ownership. Does one-human binding become friction at scale, and if
   so, at what organization size? Formal rotation may or may not be a
   sufficient answer.
2. **Time zones and continuous operation.** A 24/7 agent cannot wait
   for a single human. Rotation handles this, but the handoff
   frequency and protocol need empirical testing.
3. **Decision types that cross humans.** Some behavior-modifying
   changes logically involve multiple stakeholders (legal, security,
   product). Is "one approver per decision, informed by others" the
   right pattern, or is something more structured needed?
4. **Interaction with ADR-0004.** If one agent = one adapter = one
   human, the composition rule for multi-surface systems gets
   complex. The right grouping is not yet obvious.
5. **Revocation and departure.** What happens to an agent when its
   accountable operator leaves the organization? The handoff protocol
   must be defined; current state is unspecified.

This ADR will be revised as operational experience accumulates. The
status will move from experimental to accepted once the open
questions have documented answers, or to rejected / superseded if
testing reveals that the binding pattern is wrong.

## Consequences

- The accountability chain has a definite endpoint per agent
  instance. Postmortems do not terminate at "the team."
- Deploying an agent requires naming a human. This is a
  deployment-time cost; it is paid once and prevents ambiguity later.
- Agents cannot be "orphaned." An agent with no current accountable
  operator halts at its next approval gate, forcing the organization
  to either assign one or retire the agent.
- The binding turns agents into named tools: *Alice's research
  agent*, not *the research agent*. This shifts the mental model
  from "infrastructure" to "delegated agency," which is what the
  agent actually is.

---

**Lineage.** The germ of this principle appears in [the accountability-wall essay](https://dev.to/shimo4228/a-sign-on-a-climbable-wall-why-ai-agents-need-accountability-not-just-guardrails-17ak) (2026-04-06):
> 「責任を負える人間がループに入る構造」
> (a structure in which a human capable of bearing responsibility enters the loop)

This ADR is the first attempt to formalize that phrase as a structural rule parallel to ADR-0004. It has not yet been validated in multi-person organizational settings.
