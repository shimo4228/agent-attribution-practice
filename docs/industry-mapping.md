# Industry mechanism layer mapping

**Status:** Informational, time-bound (2026 Q2 snapshot)
**Date:** 2026-05-02
**Maintenance posture:** This file decays. The ADRs do not.

## Purpose

The ADRs in this repository record judgments about how attribution
should be distributed in autonomous AI agent systems. Through 2026
Q2, several industry releases populated the *mechanism layer* that
those judgments describe. This file tracks the mapping — at the time
of writing — between specific vendor mechanisms and the ADRs they
instantiate.

The mapping is kept in a separate file, not embedded in the ADRs
themselves, to honour the repository's core thesis: *implementation
dissolves; judgment persists*. Vendor products will be renamed,
discontinued, or restructured; the ADRs will not. Editing this file
as products evolve does not require touching the ADRs.

For the narrative spine that produced these ADRs, see
[`inspiration.md`](inspiration.md). For the high-level positioning of
AAP relative to the mechanism layer, see
[`../README.md`](../README.md#relationship-to-industry-mechanism-layer).
For terminology distinctions (sponsor vs gap-bearer, agent governance
vs accountability distribution, observability vs structural
enforcement), see [`glossary.md`](glossary.md).

## Per-ADR mapping

### ADR-0002 Deterministic prohibition at scaffolding

[Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)
(2026-04-02, MIT-licensed open source) ships *Agent OS*, a
sub-millisecond stateless policy engine intercepting all agent
actions, with stated coverage of OWASP Agentic Top 10. As a
mechanism instance for
[ADR-0002](adr/0002-deterministic-prohibition-at-scaffolding.md)'s
principle, it provides a deterministic gate that fires at the
scaffolding layer rather than relying on model-weight compliance.
It does not provide the
[prohibition-strength hierarchy](glossary.md#prohibition-strength-hierarchy)
judgment that decides which prohibitions belong at the absence layer
(ADR-0001), the scaffolding layer (this ADR), or the
untrusted-content layer (ADR-0003); product material treats all
prohibitions as scaffolding-layer concerns. The two layers are
complementary: the toolkit makes the principle technically
enforceable, the ADR records when, why, and at what cost the
principle should be enforced.

### ADR-0006 Causal traceability

[Microsoft Purview Data Security Investigations](https://learn.microsoft.com/en-us/microsoft-agent-365/overview),
shipped as part of
[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01, $15/user/month standalone or part of Microsoft 365
E7 — also referred to as the Frontier Suite), correlates logs across
Microsoft 365, Azure, and integrated third-party AI services to
reconstruct the data lifecycle of AI interactions, with automatic
AI-sensitive information classification, retention labels, and DLP
policy application. As a mechanism instance for
[ADR-0006](adr/0006-causal-traceability.md)'s causal traceability
requirement, it is the closest commercial product to the "every
event reconstructible after the fact" property. It does not provide
the argument for *why* causal traceability has to be a build-time
structural requirement rather than a runtime observability practice
(the ADR's core point), and it does not record which approval gated
which scaffolding version (the audit trail tied to ADR-0005 that the
reconstruction needs to terminate at a named human). The two layers
are complementary: Purview makes the reconstruction technically
possible, the ADR records why the reconstruction has to terminate
where it terminates.

### ADR-0007 Scaffolding visibility

Microsoft's *Shadow AI* framing accompanying
[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01) is the industry-level recognition of the absence
problem [ADR-0007](adr/0007-scaffolding-visibility.md) records: when
scaffolding is not visible, governance cannot apply. Microsoft
Defender and Intune already detect local agents on Windows devices
(OpenClaw, GitHub Copilot CLI, Claude Code) and a 2026-06 release is
scheduled to add asset context mapping (which device, which MCP
server, which cloud resource each agent touches). As a mechanism
instance, this is detection-side visibility — making previously
invisible scaffolding discoverable to operators. It does not provide
the argument that scaffolding visibility is the structural
alternative to weights internalization, the boundary between
*healthy dissolution* (scaffolding melting into clear conversational
patterns / readable files) and *unhealthy internalization*
(scaffolding absorbed into weights), or the operator-versus-public
visibility distinction the ADR draws. The two layers are
complementary: Microsoft surfaces the Shadow AI gap operationally,
the ADR records why the gap matters and where the visibility line
has to be drawn.

### ADR-0008 One agent, one human

[Microsoft Entra Agent ID](https://learn.cloudpartner.fi/posts/microsoft-entra-agent-id-agentic-identity-ai-agents),
shipped as the identity-management axis of
[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01), introduces a *sponsor system*: each agent is
assigned a responsible party (a dedicated identity category for
agents alongside users and devices) so that orphaned credentials do
not accumulate. This is a mechanism instance for
[ADR-0008](adr/0008-one-agent-one-human.md)'s *named human per
agent* requirement. It does not provide the structural commitment
that turns a sponsor into a
[pre-named gap-bearer](glossary.md#pre-named-gap-bearer)
(acknowledgement of attribution-gap absorption, legal subject
standing, contractual authority), the normative argument that the
accountable operator must be an individual rather than a team /
role / committee, or the connection to ADR-0009's design-time triage
(gap-bearer is a deployment commitment, not a runtime assignment).
The two layers are complementary: Entra Agent ID makes per-agent
ownership administratively possible, the ADR records what a
structural binding requires beyond the administrative assignment.
The [Sponsor (vendor terminology)](glossary.md#sponsor-vendor-terminology)
glossary entry records the distinction.

## Other 2026 Q2 industry artifacts (no single-ADR mapping)

The following artifacts populate adjacent layers (LLM Workflow
Quadrant tooling, alternate cloud surfaces, cross-environment
observability) without a clean one-to-one ADR mapping:

- **[Microsoft Agent Framework 1.0](https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-version-1-0/)**
  (2026-04-03). .NET / Python SDK unifying Semantic Kernel and
  AutoGen, with full MCP protocol support and A2A support announced
  at 1.0 (full A2A 1.0 support noted as forthcoming in the launch
  post), a graph-based workflow engine, and middleware hooks.
  Populates LLM Workflow Quadrant tooling alongside Anthropic's
  "Building Effective Agents" patterns. Not a single-ADR instance;
  closest to the *workflow* end of the quadrant decomposition.
- **AWS Bedrock + OpenAI integration** (announced 2026-04-28 as
  limited preview; GA described as imminent at launch). Independent
  agent-management surface separate from the Microsoft stack. The
  same judgment-layer questions apply (who is the gap-bearer, what
  is the Phase-crossing decision when an autonomous loop is placed
  in operation phase).
- **Google Vertex AI Agent Builder** (rebranded Gemini Enterprise
  Agent Platform at Google Cloud Next 2026). Independent
  agent-platform line. Included as a sync target in Microsoft's
  cross-cloud registry preview, demonstrating the fragmentation
  problem entro.security flags: each cloud provider maintains its
  own identity model, producing multiple governance blind spots that
  a registry sync surfaces but does not eliminate.
- **[entro.security](https://entro.security/blog/microsoft-agent-365-pushes-ai-identity-forward-but-enterprise-agents-still-need-cross-environment-governance/)
  and oasis.security**. Identity-security startups targeting the
  cross-environment governance gap that single-vendor products
  cannot reach (GitHub Actions, Slack, Jira, Jenkins, other
  non-Microsoft SaaS). Their core observation — *each cloud provider
  builds its own agent identity model, producing fragmented control
  and multiple governance blind spots* — sits in productive tension
  with ADR-0004's Single External Adapter principle: their mechanism
  is post-hoc observability over agents that have already acquired
  multiple external surfaces; ADR-0004 is the upstream design
  judgment that bounds the surface count at design time. Both layers
  have value; the
  [Observability vs structural enforcement](glossary.md#observability-vs-structural-enforcement)
  glossary entry records the distinction.

## AI liability insurance as an accountability market (2026 H1)

Through the first half of 2026 the insurance industry moved AI risk
out of the coverage gray zone and began underwriting it as a
distinct product — a market-side instantiation of judgments the
ADRs record at the design layer:

- **[ISO/Verisk generative-AI CGL exclusions](https://core.verisk.com/Insights/Emerging-Issues/Articles/2025/July/Week-4/Emerging-Risks-in-ISO-General-Liability-Multistate-Filing)**
  (effective 2026-01-01). Verisk's ISO introduced optional
  endorsement forms — CG 40 47 (Coverages A and B), CG 40 48
  (Coverage B only), CG 35 08 (products/completed operations) —
  letting carriers explicitly exclude generative-AI liability from
  commercial general liability policies
  ([Gallagher confirms](https://www.ajg.com/news-and-insights/iso-introduces-generative-ai-exclusion-in-commercial-general-liability-policies/)
  the forms appearing on live policies). The exclusions are
  carrier-optional, not industry-automatic; their effect is to push
  AI risk toward dedicated products.
- **[Munich Re aiSure](https://www.munichre.com/en/solutions/for-industry-clients/insure-ai.html)**.
  Performance-warranty-backed AI insurance covering defined AI
  performance failures; documented limits run to roughly $5M for
  standard model policies and up to $15M initial capacity via the
  [Mosaic partnership](https://www.mosaicinsurance.com/resources/press-releases/~/mosaic-partners-with-munich-res-aisure-to-provide-pioneering-coverage-for-ai-vendors/).
  A separate provider, AIUC, writes AI-agent policies up to $50M.
- **[Klaimee](https://www.ycombinator.com/launches/QCC-klaimee-insures-your-ai-agents)**
  (Y Combinator). Four-step chain — risk evaluation → certification
  report → financial guarantee → AI liability insurance — using
  100+ behavioral probes (prompt injection, jailbreak, decision
  drift, data leakage, biased output) to score agents on eight risk
  dimensions (scope, data exfiltration, unauthorized action, output
  integrity, adversarial manipulation, behavioral stability, model
  drift, operational control), graded A–F; the grades inform
  underwriting.
- **[Mount](https://www.ycombinator.com/launches/QUI-mount-the-ai-agent-insurance-carrier)**
  (Y Combinator, Spring 2026). Positions as "The AI Agent Insurance
  Carrier" (scan/red-team → quantify residual risk → insure). At
  the time of writing it has not publicly disclosed a fronting
  carrier or admitted-carrier authority, so "carrier" is
  self-positioning, not a verified regulatory status.

Regulatory adjacency: the Colorado AI Act
([SB 24-205](https://leg.colorado.gov/bills/sb24-205)) grants
deployers a rebuttable presumption of reasonable care where a
documented risk-management program (e.g. aligned to an AI risk
management framework or AI management system standard) exists —
provable oversight becomes a condition for favorable legal and
underwriting treatment. No jurisdiction has enacted a named
"reasonable oversight standard"; claims to that effect circulating
in 2026 commentary blend the Colorado presumption, human-oversight
obligations in EU law, and a withdrawn EU liability proposal.

The AAP reading: an insurer contractually named in advance as the
financial receiver of an agent's losses is the market formalizing
the *pre-named gap-bearer* judgment
([ADR-0008](adr/0008-one-agent-one-human.md) /
[ADR-0009](adr/0009-triage-before-autonomy.md) territory), and the
grading dimensions the market converged on independently overlap
the properties the ADRs treat as accountability-relevant (scope,
unauthorized action, behavioral stability, operational control).
The caveat below applies with full force: a policy pays for a loss;
it does not decide who should have approved the action. Whether
insurance substitutes for or presupposes the design-layer
principles (approval gates, causal traceability) is an open
question this repository tracks on the judgment side.

## ADRs without a clean 2026 Q2 mechanism instance

| ADR | Reason no clean instance |
|---|---|
| [ADR-0001](adr/0001-security-by-absence.md) Security by Absence | Available industry mechanisms run in the opposite direction (observability over already-broken designs vs structural absence at design time) |
| [ADR-0003](adr/0003-untrusted-content-boundary.md) Untrusted Content Boundary | No clean mechanism-layer instance |
| [ADR-0004](adr/0004-single-external-adapter.md) Single External Adapter | Cross-cloud registry sync runs in the opposite direction (post-hoc observability over multiplied surfaces vs design-time blast-radius bounding) |
| [ADR-0005](adr/0005-human-approval-gate.md) Human Approval Gate | No clean mechanism-layer instance |
| [ADR-0009](adr/0009-triage-before-autonomy.md) Triage Before Autonomy | Pre-deployment commitment, not a runtime mechanism |
| [ADR-0010](adr/0010-phase-separation.md) Phase Separation | Pre-deployment commitment, not a runtime mechanism |

## Caveat

The artifacts above ship *mechanism*: identity primitives for
agents, sub-millisecond policy gates, cross-vendor audit
infrastructure, sponsor systems, registry sync. They do not ship
*judgment*: who should be the sponsor and what commitment that
acknowledges, why the prohibition-strength hierarchy ranks absence
above scaffolding above untrusted boundary, when an autonomous loop
should be triaged out of operation phase, what Phase-crossing
decision a deployment is running under. The two layers are
complementary; the mechanism layer makes the principles technically
enforceable, the judgment layer (the ADRs) records when, why, and at
what cost the principles should be enforced.

Vendor products listed here will be renamed, discontinued, or
restructured. The ADRs they map to will not. Update this file as
products evolve; do not back-propagate vendor changes into the ADRs.
