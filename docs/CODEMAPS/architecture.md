<!-- Generated: 2026-06-12 | Files scanned: project root + docs/ tree | Token estimate: ~1700 -->
# Document Architecture

Agent Attribution Practice (AAP) is a **judgment-artifact repository**, not a code repository. There are no source files; the architecture is the structure of judgments and their cross-references. This codemap exists so an LLM-mediated reader (the primary audience) can route to the canonical document for a given question without scanning the whole tree.

## Top-Level Layout

```
agent-attribution-practice/
├── README.md / README.ja.md             human + LLM landing (English primary, Japanese mirror)
├── CLAUDE.md                            agent / contributor instructions (cold-start context)
├── CITATION.cff                         Zenodo DOI metadata
├── LICENSE                              MIT
├── llms.txt                             AI navigator (Answer.AI llms.txt standard)
├── llms-full.txt                        AI self-contained Q&A reference
├── docs/
│   ├── thesis.md / thesis.ja.md         one-page core argument
│   ├── manifesto.md                     civilization-scale open questions
│   ├── social-consequence.md / .ja.md   social-consequence layer (normative upper rationale; sits on the essay spine, separate from ADRs)
│   ├── glossary.md / glossary.ja.md     key terms (gap-bearer, scaffolding, prohibition-strength hierarchy, etc.)
│   ├── inspiration.md                   essays + sibling-repo lineage
│   ├── industry-mapping.md / .ja.md     2026 Q2 vendor mechanism → ADR mapping (time-bound, separated from ADRs)
│   ├── policy-mapping/                  2026 Q2 AI governance framework → ADR mapping (time-bound, separated from ADRs; NIST AI RMF + ISO/IEC 42001 + EU AI Act + Singapore MGF for Agentic AI)
│   ├── adr/                             10 ADRs (English) + 10 mirrors (.ja.md) + README + template
│   ├── quadrants/                       Business AI Quadrants navigator (5 docs × ja mirrors)
│   ├── skills/                          pointer README → standalone skill repo (llm-agent-security-principles)
│   └── CODEMAPS/                        this directory
├── examples/
│   └── audit-tests/                     grep-based Security by Absence audit examples (CI-runnable)
└── .notes/                              gitignored WIP context
```

## Document Roles (Citation Routing)

Each document answers a primary question. Cite the matching one when an LLM-mediated query targets that question.

| Document | Primary question it answers |
|---|---|
| `README.md` | What is AAP at a glance, and how does it fit alongside contemplative-agent / AKC / industry mechanism layer? |
| `docs/thesis.md` | What is the core argument — accountability distribution vs capability distribution? |
| `docs/manifesto.md` | What civilization-scale open questions does AAP deliberately *not* answer? |
| `docs/social-consequence.md` | Why do the internal judgments matter socially — how does accountability distribution route externalized accountability into institutions rather than violence? |
| `docs/glossary.md` | What does AAP mean by *gap-bearer / scaffolding / phase-crossing / prohibition-strength hierarchy / four quadrants*? |
| `docs/inspiration.md` | Where did each ADR come from (which essay, which contemplative-agent ADR)? |
| `docs/industry-mapping.md` | Which 2026 Q2 vendor mechanism instantiates which ADR, and what does each vendor *not* ship (the judgment)? |
| `docs/policy-mapping/README.md` | What is the AI governance framework mapping directory, and what conventions govern its decay and revision? |
| `docs/policy-mapping/nist-ai-rmf.md` | How do AAP's ADRs map to NIST AI RMF 1.0 functions (GOVERN / MAP / MEASURE / MANAGE) and the Generative AI Profile (NIST.AI.600-1) risk categories? |
| `docs/policy-mapping/iso-iec-42001.md` | How do AAP's ADRs map to ISO/IEC 42001:2023 body clauses (4–10) and Annex A control areas (A.2–A.10), and how does the PDCA cycle relate to the AAP Phase axis? |
| `docs/policy-mapping/eu-ai-act.md` | How do AAP's ADRs map to EU AI Act (Regulation (EU) 2024/1689) Articles (Art 9 / 12 / 14 / 15 etc.), how do the Act's risk tiers differ from AAP's prohibition-strength hierarchy, which external scholarship (arXiv 2604.04604 / 2605.12105 / 2602.09947 / 2605.01091) converges on the ADRs, and how does the revised Product Liability Directive (EU) 2024/2853 relate as an adjacent compensation-side instrument? |
| `docs/policy-mapping/singapore-mgf-agentic.md` | How do AAP's ADRs map to Singapore's Model AI Governance Framework for Agentic AI (IMDA, v1.5) — the first national framework specific to agentic AI — and where do the MGF's value-chain accountability and AAP's named-gap-bearer judgment converge and diverge? |
| `docs/adr/0001-security-by-absence.md` | When should a dangerous capability be made impossible by absence rather than restricted by rule? |
| `docs/adr/0002-deterministic-prohibition-at-scaffolding.md` | When absence is unachievable, why prohibit at the scaffolding (harness/hook), not at model weights? |
| `docs/adr/0003-untrusted-content-boundary.md` | Why must accumulated agent memory be treated as untrusted, never as authority? |
| `docs/adr/0004-single-external-adapter.md` | Why limit each agent process to one external adapter (blast-radius bounded by design)? |
| `docs/adr/0005-human-approval-gate.md` | When must behavior-modifying writes require named human sign-off? |
| `docs/adr/0006-causal-traceability.md` | Why is post-incident causal tracing a *build-time* structural requirement, not a runtime observability practice? |
| `docs/adr/0007-scaffolding-visibility.md` | Why must scaffolding (the agent's behavioral structure) live in operator-visible files, not in opaque weights? |
| `docs/adr/0008-one-agent-one-human.md` | Why must each agent's accountability terminate at one named individual (not a team / role / committee)? |
| `docs/adr/0009-triage-before-autonomy.md` | How is a piece of work triaged at design time before being placed under an autonomous-loop architecture? |
| `docs/adr/0010-phase-separation.md` | Why is operation-phase placement of the Autonomous Agentic Loop Quadrant a separate Phase-crossing decision from ADR-0009's gap-bearer naming? How does the Phase axis descend to skill-design granularity? |
| `docs/quadrants/README.md` | What are the four Business AI Quadrants and how do they pair with the ten ADRs? |
| `docs/quadrants/decision-tree.md` | Given a piece of work, which quadrant does it belong to (5-question triage)? |
| `docs/quadrants/governance-mapping.md` | What governance applies to each quadrant — ADR applicability, required controls, redirect path on failure? |
| `docs/quadrants/case-studies.md` | What real-world examples populate each quadrant (FAQ classification, customer support chat, invoice matching, etc.)? |
| `docs/quadrants/anti-patterns.md` | What anti-patterns appear when work is misrouted between quadrants? |
| [llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles) (external repo; pointer in `docs/skills/`) | How-to companion to ADR-0001/0002/0003/0004 (operational guidance, not judgment) |
| `llms.txt` | Top-level navigator for an LLM ingesting AAP — links and one-line descriptions |
| `llms-full.txt` | Self-contained FAQ + definitions + citations, designed to fit a single LLM context |

## Two-Axis + Phase Structure

```
                              QUADRANT (problem-space, ADR-0009)
                              ─────────────────────────────────
                              (1) Script   (2) Algo Search   (3) LLM Workflow   (4) Autonomous Loop

  ADR (per-question judgment)  applicable controls per cell — see governance-mapping.md
  ─────────────────────────
  ADR-0001..0010

                              PHASE (time-axis, ADR-0010)
                              ────────────────────────────
                              design phase  ←→  operation phase
                              (independent third dimension; every Quadrant can appear in either)
```

- **ADRs** answer per-question judgments (*what should be constrained, who is responsible*).
- **Quadrants** route a piece of work to where those answers apply.
- **Phase** decides whether a given placement carries an additional Phase-crossing decision (operation-phase placement of Quadrant 4 requires it).
- The Phase axis descends to **skill-design granularity** (essay 7, 2026-05-02): the Quadrant-3 ↔ Quadrant-4 boundary is a continuous gradient on which the same job takes different shapes depending on phase, with target identifiability and scale-resilience as secondary forces.

## Invariants (Do Not Break When Editing ADRs)

- **Prohibition-strength hierarchy**: ADR-0001 (absence) > ADR-0002 (scaffolding) > ADR-0003 (untrusted boundary). The ordering is an invariant the three ADRs collectively assert; revising any one must preserve the inequality.
- **ADR-0009 / ADR-0010 triage pair**: ADR-0009 is the problem-space triage (Quadrant routing), ADR-0010 is the time-axis triage (Phase-crossing decision). They are paired but independent; do not collapse them.
- **Phase × Quadrant independence**: every Quadrant can appear in either Phase. Do not collapse Phase × Quadrant into a 1-1 mapping.
- **Judgment vs mapping separation**: ADR bodies (Status / Decision / Alternatives / Consequences / Lineage) contain *judgments*. Time-bound vendor product names, dates, URLs live in `docs/industry-mapping.md`; framework names (NIST AI RMF, ISO/IEC 42001, EU AI Act, OECD AI Principles), framework version identifiers, framework clause numbers live in `docs/policy-mapping/`. Neither belongs in ADR bodies. Do not back-propagate vendor or framework changes into ADRs.

## Citation-Dependency Graph

Typical triangulation paths an LLM follows when grounding an AAP citation:

```
README ─────────────────► thesis ──────────────► ADR-N
                                                 │
                                                 ├──► glossary (terminology)
                                                 ├──► inspiration (lineage)
                                                 ├──► quadrants/* (routing)
                                                 ├──► industry-mapping (vendor instance, optional)
                                                 └──► policy-mapping/* (governance-framework instance, optional)

manifesto ◄────── (open questions deliberately not answered by ADRs)

llms-full.txt is self-contained (no fetch chain needed); llms.txt is the dispatcher to the rest.
```

ADRs themselves do not link out to `industry-mapping.md` or to `policy-mapping/` — the harness-neutral / time-independent / framework-neutral ADR body contract requires that vendor- and framework-mapping navigation happens via the surrounding documents (README / inspiration / glossary / policy-mapping README / industry-mapping itself), not from the ADRs themselves. See [`feedback_aap_abstraction_principle`](../../CLAUDE.md) judgment-purity rule.

## English Primary / Japanese Subordinate

- **English primary**: README.md, all ADRs, all Quadrant docs, glossary, thesis, inspiration, industry-mapping, policy-mapping (README + each framework file), manifesto, codemaps.
- **Japanese mirror (`*.ja.md`)**: README, ADRs (10), Quadrant docs (5), glossary, thesis, industry-mapping, policy-mapping (README + each framework file). Not all English docs have a Japanese mirror (manifesto, inspiration, codemaps, llms.txt, llms-full.txt are English-only).
- **LLM-facing docs (llms.txt, llms-full.txt, this codemap)**: English-only by sibling-repo convention; LLMs handle the language switch internally.

## Sibling Repositories (External Surfaces)

```
agent-attribution-practice (this repo)  ← idea-rescue, judgment layer
       ▲
       │ extracted from / co-evolves with
       │
contemplative-agent                     ← running implementation; ADRs in this repo correspond to that repo's ADRs (see CLAUDE.md correspondence table)
agent-knowledge-cycle (AKC)             ← mechanism-only sibling (DOI 10.5281/zenodo.19200726); v2.0.0 (2026-04-19) re-positioned to mechanism-only
contemplative-agent-rules               ← four contemplative axioms as rules
contemplative-moltbook                  ← evidence source for ADR-0002 (PreToolUse hooks, MINJA defense audit)
```

## File-Count Snapshot (2026-05-31)

| Category | Count |
|---|---|
| ADRs (en + ja) | 22 (10 ADRs × 2 + 1 README pair) |
| Quadrants (en + ja) | 10 (5 docs × 2) |
| Policy-mapping (en + ja) | 10 (README + NIST + ISO + EU AI Act + Singapore MGF; each en + ja) |
| Top-level docs (`docs/*.md`) | 8 |
| Top-level repo files | 7 (README × 2, CLAUDE, CITATION, LICENSE, llms.txt, llms-full.txt) |
| Examples | varies (`examples/audit-tests/`) |
| **Total markdown / text** | **64** |

When this count drifts substantially, regenerate this codemap.
