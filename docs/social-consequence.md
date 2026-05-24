Language: English | [日本語](social-consequence.ja.md)

# Social-Consequence Layer — Accountability as Violence Prevention

> A normative, social-theoretical layer that sits **on top of** the
> internal accountability-distribution judgments recorded in the ADRs.
> It adds no specification. It answers a different question: not *how*
> accountability is distributed inside a system, but *what happens in
> society when it is not* — and therefore *why* the internal judgments
> matter beyond audit and post-mortem.

## What this layer is, and is not

The ten ADRs and the four Business AI Quadrants answer an internal
question: across an agent-equipped system, what should be constrained,
and who is responsible. They are verifiable — a log exists or it does
not, an approver is named or not, a Phase-crossing decision is recorded
or not.

This layer is different in kind. It is the upper rationale: the claim
that the internal judgments are not only governance hygiene but a
mechanism for routing social consequence. It is **normative and
social-theoretical**, and therefore harder to verify empirically than
the ADRs it sits above. It changes no ADR, adds no control, and is not
part of the seven-essay spine. It explains why the spine's judgments
are socially load-bearing.

Its source is a companion essay (see Lineage). The concrete cases that
ground it — industrial pollution, the scapegoat mechanism, specific
incidents — live in that essay. This document keeps to the structural
claim.

## The reframing

**Before.** Architectural principles for tracing and distributing
accountability across an autonomous-agent system, for the sake of
internal control and post-incident reconstruction.

**After.** A socio-technical framework for ensuring that the
accountability *externalized* by AI flows into institutions rather than
into violence.

The accountability-distribution thesis has a structural sibling:

> Capability distribution is not accountability distribution.

This layer adds its social-consequence corollary:

> **Accountability distribution is not only governance. It is a
> violence-prevention mechanism.**

## Why externalized accountability does not disappear

When a system produces benefit privately and pushes harm outward, the
ability to say *whose judgment it was* is externalized along with the
harm. That ability does not vanish. It takes one of two paths:

- **Nameable.** The harm can be attributed to a specific actor's
  specific act, and an institution can take it up. Anger flows into
  litigation, compensation, regulation. Slow, heavy — but not violent.
- **Un-nameable.** The harm is diffuse, and *whose judgment it was* — or
  even *what happened* — cannot be traced. The anger does not
  disappear; it converges on the most visible single individual.

Whether a consequence is nameable is decided in two steps: first,
whether a record of what happened exists; second, whether an
institution has a place for the actor who must bear the harm. The
internal judgments recorded in the ADRs are precisely what supply both
steps. That bridge is what this layer makes explicit.

## Three things the layer changes about the existing judgments

It adds no rule. It re-weights the meaning of three judgments already
in the repository.

1. **The purpose expands.** The accountability-distribution practice was
   framed as internal control and post-mortem: *who can explain after
   an incident*. This layer reframes it as social infrastructure — a
   channel that routes social anger into institutions instead of onto a
   single face. Traceability is not only an audit property; it is a
   violence-prevention property.

2. **The minimum disclosure set becomes an evidence base.** The minimum
   set that makes causal tracing possible — which configuration was
   active, which inputs reached the agent, which approval gated it
   ([ADR-0006](adr/0006-causal-traceability.md),
   [ADR-0007](adr/0007-scaffolding-visibility.md)) — is not only a
   technical log. It is the equivalent of an effluent record: the
   evidence base a third party needs to attribute causation at all.
   Without it, the burden of proof falls entirely on those harmed, and
   the consequence stays on the un-nameable side.

3. **The gap-bearer becomes an institutional receptacle.** A pre-named
   gap-bearer ([ADR-0008](adr/0008-one-agent-one-human.md),
   [ADR-0009](adr/0009-triage-before-autonomy.md)) was framed as the
   internal endpoint of the accountability chain. This layer reframes
   it: a face named in advance, by design, is the structural opposite of
   a scapegoat — the face that emerges *because none was named*. Naming
   the bearer at deployment time is what lets a consequence pass through
   an institution instead of converging violently on whoever is most
   visible afterward.

## Boundary

This layer is a normative extension, not an existence proof. Unlike the
ADRs — each grounded in a concrete implementation — its central claim
(nameable consequence routes to institutions; un-nameable consequence
routes to violence) is a social-theoretical reading supported by
historical analogy, not by a reproducible test. Treat it as upper
rationale, not as a specification to comply with.

It is also distinct from the [open questions](manifesto.md). The
manifesto collects civilization-scale questions the ADRs do **not**
answer. This layer makes a claim the ADRs' existing answers already
*serve* — it is rationale for the answered, not a catalogue of the
unanswered.

## Lineage

This layer was first written up as a companion essay,
**"Where Does the Accountability Externalized by AI Go?"**, published on
Substack and mirrored in the content repository
([EN](https://github.com/shimo4228/zenn-content/blob/main/substack/ai-externalized-accountability-pollution-en.md)
· [日本語](https://github.com/shimo4228/zenn-content/blob/main/substack/ai-externalized-accountability-pollution.md)).
The essay carries the concrete grounding (the pollution analogy, the
scapegoat mechanism, the specific incidents); this document carries the
structural claim. It sits on top of the
[seven-essay narrative spine](inspiration.md): essays 1–7 describe the
internal structure; this is where the argument exits into social
consequence. The two companion position papers cited in the
[README](../README.md) distil the internal argument; this layer extends
it outward.
