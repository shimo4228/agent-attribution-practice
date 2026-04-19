# Manifesto — The Open Questions

> **Status.** Draft / in-progress. This document captures the civilization-scale
> questions that motivate the eight ADRs but that the ADRs themselves do not
> try to answer. It is written as a provocation, not a position paper, and is
> expected to be revised as the discussion develops.

## Why this document exists

The eight ADRs in this repository are partial answers at the scale of an
individual agent instance. They describe how to structurally distribute
accountability across a running system. They do not — and cannot — answer
larger questions:

- **Why does AI have more unchecked authority than any individual human has
  ever been given?** Human societies spent three centuries building
  containment for powerful capable actors — constitutional limits
  (Locke, 1690), separation of powers (Montesquieu, 1748), federated
  checks and balances (Madison, 1788), due process, term limits,
  antitrust, audit, free press, impeachment. AI agents in 2026 routinely
  ship without any of these. Why?

- **Who authorized the current direction?** The decisions that most
  shape where AGI development goes — training objectives, deployment
  pace, which capabilities get built, which markets are entered —
  are made by a small number of actors: a handful of AI research labs,
  their investors, a segment of the AI-policy community.
  Social consent mechanisms comparable to those governing nuclear,
  financial, biomedical, or aviation domains are absent. Who agreed
  to this arrangement, and on what authority?

- **Why is the deployment imagination so narrow?** AI has transformative
  potential that in principle reaches the foundations of capitalism itself
  — labor, ownership, value exchange. Yet actual deployment is almost
  entirely shaped by capital scale-logic: productivity tools for existing
  workflows, within existing economic relations. The possibility that
  this technology could warrant a grand redesign of human labor is
  raised rarely, and mostly at the margins. Why are we scaling without a
  grand design?

- **What is the pace-mismatch cost?** Technology change is exponential;
  social-structure change is linear. Historically, every major technology
  that outran its social-structure counterpart — industrial revolution,
  finance, nuclear energy, biomedical research — paid for the gap in
  some currency (child labor, market crashes, Hiroshima, Tuskegee). AI is
  currently running the widest pace mismatch of any technology in modern
  history. What is the gap-period going to cost, and who pays it?

- **What grounds attribution when authorship itself is unstable?**
  Attribution presupposes authorship. In a self-improving pipeline
  where the operator designs the cognitive substrate, controls the
  experiential environment, and mediates every self-reflective
  artifact, "whose voice is this?" has no clean answer. The eight
  ADRs proceed as if the prior question were settled; it is not.
  The working materials that surface this problem remain private —
  deliberately.

## Historical parallels

| Technology | Pace mismatch | Social catchup | Interval |
|------------|---------------|----------------|----------|
| Industrial revolution | Child labor, 14-hour days | Labor law, unions, social insurance | Century |
| Finance | 1929 crash, 1987, 2008 | SEC (1934), Glass-Steagall, Dodd-Frank | Decades |
| Nuclear energy | Hiroshima, Chernobyl | IAEA (1957), NPT (1968) | 30–50 years |
| Biomedicine | Tuskegee, thalidomide | Belmont (1979), IRBs, GCP | Decades |
| Aviation | Tenerife, others | ICAO, NTSB, mandatory reporting | Decades |

The one partial exception, biotechnology, is worth naming: the 1975
**Asilomar Conference** was convened by the research community itself,
moratorium first, before the external pressure. It is the rare case of
pace-matching achieved voluntarily by the people closest to the work.

## What this repository claims

Only that individual agent instances can be built with structural
accountability, as an existence proof. The eight ADRs demonstrate that
the technical pieces exist and compose. The demonstration is narrow.

It does not claim that this solves the larger questions above. It
claims that the larger questions are open, that pretending they are
closed is itself part of the problem, and that implementation-side
proofs-of-concept are one input among many to how the larger
discussion might eventually move.

## Who this document is addressed to

- **Practitioners** who already know the individual agent pieces work
  and want vocabulary for the harder argument.
- **Policy people** encountering this repository from the governance
  side, looking for concrete artifacts to ground abstract discussion.
- **Future readers** who may inherit whatever mess the pace mismatch
  produces, and who may find it useful to know that the problem was
  at least named at the time.

This document is deliberately not addressed to the AI radicals whose
current consent-scope is small and whose deployment pace is fast. They
are welcome to read, but they are not the audience.

---

**Status note (repeated).** This is draft. The document is shaped
through discussion rather than dictated. Contributions and challenges
at the issue tracker are welcome. Expect revisions.
