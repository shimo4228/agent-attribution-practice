# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Third standalone Agent Skill repository: [`agent-observability-patterns`](https://github.com/shimo4228/agent-observability-patterns) — three skills (replayable-audit-logs, read-only-instruments, shadow-mode-validation) packaging observation-precedes-intervention design patterns as the installable "how" counterpart to ADR-0006 Causal Traceability (replayable logs supply the post-incident reconstruction substrate) and ADR-0005 Human Approval Gate (instruments and shadow records supply the evidence a named approver signs off from). Generalized from the operational variant inside contemplative-agent; manually curated, no automated sync. Cross-linked from `docs/skills/README.md` / ADR-0005 / ADR-0006 (en + ja) / `README` ×2 / `docs/CODEMAPS/architecture.md` / `llms.txt`, and federated into the machine-readable layer: a `SoftwareSourceCode` node with `isBasedOn` edges to ADR-0006/0005 in `graph.jsonld`, the *Skill references* answer in `llms-full.txt`, an `isSourceOf` entry in `.zenodo.json`, and the skill-repository count in `codemeta.json`.

## [0.6.0] — 2026-07-06

Governance-framework coverage grew from three frameworks to four (EU AI Act, then Singapore MGF for Agentic AI); the security-principles skill was externalized to a standalone repo and joined by a second standalone skill repo (agent-adoption-triage); the citation / identifier federation was extended (harness-alignment paper, Wikidata cited-work QIDs, AI-native preprint placements, SWHID, CodeMeta); the README pair was fully rewritten. No ADR added or changed in number or content; one Quadrant-doc description of ADR-0005 was corrected to match the ADR body.

### Added

- `docs/policy-mapping/eu-ai-act.md` (+ `.ja.md`) — EU AI Act (Regulation (EU) 2024/1689) mapping alongside the existing NIST AI RMF and ISO/IEC 42001 files. Per-ADR mapping of all ten ADRs to the Act's Articles (Art 5 risk tiers, Art 9 risk management, Art 10 data governance, Art 11/Annex IV documentation, Art 12/19 record-keeping, Art 13 transparency, Art 14 human oversight, Art 15 robustness/cybersecurity, Art 17 quality management, Art 26–27 deployer duties, Art 72 post-market monitoring), plus a reverse index (Article → ADR). Distinguishes the Act's four risk tiers from AAP's prohibition-strength hierarchy as graduated structures that grade *different things* (system danger vs single-capability foreclosure), not an identity. A *Secondary scholarship and external convergence* note records three 2026 sources that independently arrive at — or supply mechanism for — AAP judgments: Nannini et al., *AI Agents Under EU Law* (arXiv:2604.04604); Safin & Balta, *Autonomy and Agency in Agentic AI: Architectural Tactics for Regulated Contexts* (arXiv:2605.12105); Bhattarai & Vu, *Trustworthy Agentic AI Requires Deterministic Architectural Boundaries* (arXiv:2602.09947). They are kept as external citation surface, explicitly outside the ADR lineage.
- `docs/policy-mapping/singapore-mgf-agentic.md` (+ `.ja.md`) — Singapore Model AI Governance Framework for Agentic AI (IMDA, v1.5 2026-05) mapping, the fourth framework in the directory and the first national framework specific to agentic AI. Records the directory's strongest convergence (the MGF's structural-vs-prompt-layer control preference ↔ ADR-0002) and an explicit tension (the MGF answers accountability diffusion with a value chain; AAP records that a chain distributes but does not terminate — ADR-0008 / 0009 add the terminal designation).
- Two standalone Agent Skill repositories now carry the installable "how" counterpart to the ADRs: [`llm-agent-security-principles`](https://github.com/shimo4228/llm-agent-security-principles) (ADR-0001..0004; externalized from `docs/skills/`) and [`agent-adoption-triage`](https://github.com/shimo4228/agent-adoption-triage) (ADR-0009/0010; the `docs/quadrants/` navigator in installable form). Cross-linked from `README` ×2 / `docs/CODEMAPS/architecture.md` / `docs/skills/README.md` / `llms.txt`, and federated into the machine-readable layer: `SoftwareSourceCode` nodes with `isBasedOn` edges to their source ADRs in `graph.jsonld`, a *Skill references* section in `llms-full.txt`, and `isSourceOf` entries in `.zenodo.json`.
- `codemeta.json` — CodeMeta software-metadata surface alongside `CITATION.cff`.
- LLM-queryable badges on the README pair: Ask DeepWiki and GitMCP (a Google Code Wiki badge was added and reverted — generation is not author-controllable).
- Citation / identifier federation extensions: the AKC-line *Harness Alignment and Harness Drift* paper (DOI 10.5281/zenodo.20578272) cited across `graph.jsonld` / `.zenodo.json` / Wikidata; Wikidata QIDs for six cited-work items (P2860 federation); four external-reference citation nodes from wiki-harvest plus five corroborating arXiv citations synced across docs / graph / zenodo; AI-native preprint platform placements (aiXiv, AiraXiv) added as `sameAs` on both companion-paper nodes; Software Heritage SWHID recorded (intrinsic-identifier layer); `ResearchLine` linked up to the ecosystem hub via `isPartOf`.

### Changed

- `README.md` / `README.ja.md` — **full rewrite** (353 → ~250 lines), entry-focused: the two companion papers now appear exactly once (previously listed twice — front block and "Related publication"), essays and papers merged into one section, the two long industry-/framework-relationship sections compressed into a pointer-style *External mappings* section (deep content unchanged in `docs/industry-mapping.md` / `docs/policy-mapping/`), the ASCII layer diagram replaced with a Mermaid diagram plus one-sentence text equivalent, a worked triage example added to the navigator section, glossary added to the reading order, ORCID added to Origin, and a concept-vs-version DOI note added to How to Cite. Corrected a stale claim that ADRs are English-only (all ten ship `.ja.md` mirrors since 2026-05-01).
- `docs/policy-mapping/README.md` (+ `.ja.md`): EU AI Act and Singapore MGF added to the files table; the deferred-frameworks note narrowed to OECD AI Principles only.
- `graph.jsonld`: new EU AI Act + Singapore MGF `PolicyFramework` nodes and mapping-document nodes appended to all ten ADRs' `mappedToFrameworkIn`; wording shift "research program" → "research ecosystem" (the author is a practitioner, not a researcher).
- `CLAUDE.md`: read-only research-wiki consultation protocol added; local clone reference updated.

### Fixed

- **ADR-0005 description drift in the Quadrant docs**: `docs/quadrants/governance-mapping.md` (+ `.ja.md`) claimed Quadrant 4 requires "human approval on **every external action** (0005)" — a stronger requirement than ADR-0005's body, which gates *behavior-modifying writes* and explicitly rejects per-action gating in Alternatives Considered. Both the at-a-glance table and the Q4 detail row now match the ADR body (and the standalone `agent-adoption-triage` skill, which was already faithful). Two `docs/quadrants/case-studies.md` (+ `.ja.md`) entries were rephrased the same way: external-action gating is now presented as a deployment-specific choice *beyond the ADR floor*, not as an ADR-0005 requirement.
- `docs/quadrants/case-studies.md`: two stale "All nine" ADR counts updated to "All ten" (the `.ja.md` mirror had already been corrected; the English file drifted behind).
- `CLAUDE.md`: stale "7 つの判断" corrected to "10 の判断".
- Hashimoto *My AI Adoption Journey* citation year corrected to 2026.
- EU AI Act node dates in `graph.jsonld` synced with the Omnibus postponement.

### Notes

- **No new judgments**: ADR bodies remain harness / vendor / framework-neutral; EU AI Act and Singapore MGF content lives only in the time-bound `policy-mapping/` directory. The architectural tactic *write staging* (Safin & Balta) is recorded as *mechanism* in the mapping layer and deliberately **not** folded into ADR-0005's body — consistent with *implementation dissolves; judgment persists*.
- **Faster decay**: the EU AI Act file is expected to decay faster than NIST/ISO through the 2026–2027 phase-in (delegated acts, implementing acts, harmonised standards, Commission guidelines still arriving).
- **The ADR-0005 fix is a description correction, not a judgment change**: the ADR body is untouched; the Quadrant docs drifted stronger than the decision they summarize. The standalone skill repo was treated as the faithful reading.

## [0.5.0] — 2026-05-24

Social-consequence layer added on top of the seven-essay narrative spine: a normative, social-theoretical upper rationale reflecting the companion Substack essay. No ADR added or changed; the ten ADRs and four Quadrants are unchanged in number and content.

### Added

- `docs/social-consequence.md` (+ `.ja.md`) — the social-consequence layer's canonical structural claim: externalized accountability does not disappear, and whether it routes into institutions (litigation, compensation, regulation) or converges into violence depends on whether the consequence can be named. Reframes accountability distribution as a violence-prevention mechanism, the minimum disclosure set as an evidence base (effluent-record equivalent), and the pre-named gap-bearer as an institutional receptacle rather than a scapegoat. Harness-neutral and concept-level; concrete cases deferred to the essay.
- New term *externalized accountability* in `docs/glossary.md` (+ `.ja.md`).
- `graph.jsonld`: new `externalized-accountability` concept node plus a node for the companion Substack essay *Where Does the Accountability Externalized by AI Go?* — deliberately carrying no `narrativePosition` and excluded from the seven-essay spine Collection, so the layer's separation is enforced at the schema level. Added to the project node's `definesConcept`.
- Social-consequence layer surfaced across the AI-facing and human-facing docs: `llms.txt`, `llms-full.txt`, `README.md` / `README.ja.md`, a `docs/thesis.md` (+ `.ja.md`) corollary, `docs/inspiration.md`, a `docs/manifesto.md` boundary cross-reference, `CLAUDE.md`, and `docs/CODEMAPS/architecture.md`.

### Changed

- `docs/inspiration.md`: the companion essay reframed from "a derivative, not part of the spine" to the normative upper rationale that sits on top of the spine.
- Stale-count corrections across docs: "eight ADRs" → "ten" (thesis, manifesto, llms-full.txt), "six essays / six-essay" → "seven" (thesis, README, `.zenodo.json`, `CITATION.cff`). Corrected the Agent Knowledge Cycle governance-triplet description in `README.md` / `README.ja.md` (three ADRs — Security by Absence, Single External Adapter, Untrusted Content Boundary — not "the first eight").

### Notes

- **No new judgments**: the ten ADRs and four Quadrants are unchanged. The social-consequence layer is normative / social-theoretical upper rationale kept separate from the ADRs — it changes no ADR body and adds no control. The ADRs' harness / vendor neutrality is preserved (the essay's concrete socio-political cases live outside the repo). CODEMAPS structure otherwise unchanged.
- **DOI update follows tag push**: the v0.5.0 versioned DOI is assigned by Zenodo after the GitHub Release object is created. `CITATION.cff` `doi:` / `url:`, the README BibTeX `doi` / `url`, the README "How to cite" line, and the `llms.txt` / `llms-full.txt` DOI fields are updated in a small follow-up commit once Zenodo numbers the v0.5.0 record. The concept DOI (10.5281/zenodo.19652013) in the README badge does not change between releases.

## [0.4.0] — 2026-05-23

Two companion position papers federated into the citation graph, plus the LLM Workflow Quadrant sub-form refinement and DOI / sibling-reference plumbing accumulated since v0.3.0. No ADR added or changed.

### Added

- Two companion position papers cross-linked into the federation, both deposited to Zenodo (CC BY 4.0):
  - *Distributing Accountability, Not Capability: Phase Separation and the LLM Workflow Quadrant in Autonomous AI Agent Architectures* (essays 4–7, architectural follow-up) — concept DOI 10.5281/zenodo.20353789.
  - *The Two-Layer Black Box: Operator Visibility, Commercial Secrecy, and a Minimum Disclosure Set for Accountable Autonomous AI Agents* (essays 1–3, foundational trilogy) — concept DOI 10.5281/zenodo.20355907.
  - Both surfaced in `README.md` / `README.ja.md` "Related publication", as `ScholarlyArticle` nodes in `graph.jsonld` with the project node's `subjectOf` edge, in `.zenodo.json` as `isReferencedBy`, and in `llms-full.txt`.
- `.zenodo.json` added with `related_identifiers` (sibling repos + both companion papers) so the relationships propagate to the repo's own Zenodo metadata on release.
- Sibling-reference enrichment in `CITATION.cff` (DOI + notes for related shimo4228 research lines).

### Changed

- LLM Workflow Quadrant (Quadrant 3) reframed around its load-bearing property — the path is decided in advance; the LLM is called as a single bounded step within it — and decomposed into the **(3a) Conversational** and **(3b) Batch** sub-forms, propagated across `docs/glossary.md`, `docs/quadrants/`, ADR-0009, `graph.jsonld` (sub-quadrant nodes + the `LLM function` concept), `llms.txt`, and `llms-full.txt`. Verified verbatim against essays 4–5 as primary sources.
- AKC concept DOI references corrected (10.5281/zenodo.19200727 → 19200726).
- `CLAUDE.md` records the Hugging Face Datasets mirror mapping for `graph.jsonld` (LLM-training / knowledge-graph ingest source).

### Notes

- **No new judgments**: the ten ADRs and four Quadrants are unchanged in number and content. This release captures content refinement (Q3 sub-form) and the citation-graph federation of the two companion papers, not new decisions. CODEMAPS unchanged (architecture identical).
- **DOI update follows tag push**: the v0.4.0 versioned DOI is assigned by Zenodo after the GitHub Release object is created. `CITATION.cff` `doi:` / `url:`, the `README` BibTeX `doi` / `url`, the README "How to cite" line, and the `llms.txt` / `llms-full.txt` DOI fields are updated in a small follow-up commit once Zenodo numbers the v0.4.0 record. The concept DOI (10.5281/zenodo.19652013) in the README badge does not change between releases.

## [0.3.0] — 2026-05-17

AI governance framework mapping directory (Phase 1: NIST AI RMF + ISO/IEC 42001).

### Added

- `docs/policy-mapping/` directory containing a README and per-framework mapping files for two AI governance frameworks:
  - `docs/policy-mapping/nist-ai-rmf.md` (+ `.ja.md`) — per-ADR mapping to NIST AI Risk Management Framework 1.0 (NIST.AI.100-1, 2023-01) functions (GOVERN / MAP / MEASURE / MANAGE) and Generative AI Profile (NIST.AI.600-1, 2024-07) risk categories; reverse index from NIST function and GAI risk category to applicable ADRs.
  - `docs/policy-mapping/iso-iec-42001.md` (+ `.ja.md`) — per-ADR mapping to ISO/IEC 42001:2023 body clauses (4–10, Annex SL Harmonized Structure) and Annex A control areas (A.2–A.10); includes a PDCA × Phase Separation section discussing how ADR-0010's Phase axis sits inside the AIMS PDCA cycle; reverse index from ISO area to applicable ADRs.
  - `docs/policy-mapping/README.md` (+ `.ja.md`) — directory conventions, decay disclaimer (`"This directory decays. The ADRs do not."`), framework version pinning, revision procedure, non-attestation disclaimer template, sibling relationship to `docs/industry-mapping.md`.
- `PolicyFramework` node type in `graph.jsonld` with two instances (NIST AI RMF, ISO/IEC 42001:2023) carrying `identifier`, `versionInfo`, `datePublished`, `publisher`, `url`, and `recordedIn` edges to the corresponding mapping files. Added `mappedToFrameworkIn` edges from each ADR (0001–0010) to both framework mapping files. Three new `ExternalReference + CreativeWork` nodes for the policy-mapping directory and the two framework files. Control / subcategory-level node creation deliberately avoided to keep graph size bounded; mapping is file-level only.
- "Relationship to AI governance frameworks" section in `README.md` (+ `README.ja.md`) added after the existing industry mechanism layer section.
- "Policy framework mapping" section in `llms.txt` and a parallel Q&A in `llms-full.txt` added alongside the existing industry mechanism layer entries.
- `docs/policy-mapping/` entries added to `docs/CODEMAPS/architecture.md` Document Roles table, Top-Level Layout, and English / Japanese pairing section; file-count snapshot incremented (56 → 62).

### Changed

- `CLAUDE.md` invariant section renamed to "ADR の harness / vendor / framework 中立性" and extended to forbid vendor product names (Microsoft Agent Governance Toolkit, Purview, Entra Agent ID, etc.) and framework / standard names (NIST AI RMF, ISO/IEC 42001, GOVERN function, AI Management System, etc.) in ADR bodies. Vendor instances stay in `docs/industry-mapping.md`; framework mappings stay in `docs/policy-mapping/`. Vendor / framework changes are not back-propagated into ADRs.
- `docs/CODEMAPS/architecture.md` Invariants extended: "Judgment vs mapping separation" now names both the vendor-name exclusion (industry-mapping) and the framework-name exclusion (policy-mapping). Citation-Dependency Graph extended with an `policy-mapping/*` branch.

### Notes

- **Phase 1 only**: this release ships NIST AI RMF and ISO/IEC 42001 mappings. EU AI Act and OECD AI Principles are deferred to a later release because their decay cadences and clause granularity differ enough that bundling all four in one release would create churn. EU implementing acts in particular are in flux.
- **Fact-checked**: framework-level claims (NIST identifiers and publication dates, the four NIST RMF functions, the seven GAI Profile risk categories used in the reverse index, ISO/IEC 42001:2023 publication date, Annex SL Harmonized Structure shared across ISO 27001 / 9001 / 14001, body clauses 4–10 names, PDCA as the foundation of Annex SL standards, Annex A area names A.2–A.10, ISO 5-year revision cadence) were verified against primary and authoritative secondary sources before release.
- **Non-attestation**: the mapping is offered as AAP's reading and as a citation surface for cross-reference; it is not a compliance attestation, not legal advice, and authoritative interpretation of any framework remains with its standards body (NIST, ISO/IEC) and with qualified legal / compliance counsel.
- **DOI update follows tag push**: the v0.3.0 versioned DOI is assigned by Zenodo after tag push. `CITATION.cff` `doi:` and `README.md` BibTeX `doi:` / `url:` are updated as a small follow-up commit once Zenodo numbers the v0.3.0 record. The concept DOI (10.5281/zenodo.19652013) in the README badge does not change between releases.

## [0.2.0] — 2026-05-01

Architectural expansion: triage taxonomy + Phase axis + adoption navigator + bilingual mirrors.

### Added

- ADR-0009 Triage Before Autonomy (experimental) — problem-space triage along the four Business AI Quadrants placed before ADRs 0001–0008 in the order of application.
- ADR-0010 Phase Separation Between Design and Operation (experimental) — design / operation Phase as an independent third dimension; operation-phase placement of an Autonomous Agentic Loop component requires a recorded Phase-crossing decision.
- `docs/quadrants/` adoption navigator — `README.md`, `decision-tree.md`, `governance-mapping.md`, `case-studies.md`, `anti-patterns.md`.
- Japanese mirrors for all ADRs (0001–0010), the full Quadrants navigator, `docs/thesis.md`, and `docs/glossary.md`.
- Extended `docs/glossary.md` covering the four Business AI Quadrants, attribution mechanics (attribution gap, principled vs artificial redirect impossibility, moral crumple zone), and Phase vocabulary.
- Three additional narrative essays linked from `docs/inspiration.md` (2026-04-29 Quadrant decomposition, 2026-04-30 vocabulary diagnosis, 2026-05-01 Phase distinction); narrative spine grows from three to six essays.
- AAP itself surfaced as a sibling project of `contemplative-agent` and `agent-knowledge-cycle` under a mutual-feedback two-layer model (`CLAUDE.md`, `README.md`, `docs/inspiration.md`).

### Changed

- Repository name confirmed as **Agent Attribution Practice (AAP)**; directory and GitHub URL aligned to `agent-attribution-practice`.
- Sibling-projects framing rewritten as a co-evolving mutual-feedback loop across an implementation layer (`contemplative-agent`) and a theory layer (AKC for mechanism, AAP for content).
- Scaffolding vs harness re-clarified as overlapping but distinct terms (Davidson et al. 2023 / Hashimoto 2025); ADR-0007 chooses *scaffolding* because inspectability is load-bearing for accountability distribution.
- Cross-doc deduplication: canonical home + link defer pattern applied across `docs/thesis.md`, `docs/glossary.md`, the Quadrants navigator, and the ADR set.
- Narrative-essay links migrated from zenn / dev.to to the GitHub `zenn-content` mirror.
- Zenodo DOI corrected from `10.5281/zenodo.19608386` to `10.5281/zenodo.19652014` for the v0.1.0 record.
- `llms.txt` / `llms-full.txt` extended to cover the ten ADRs, the four Business AI Quadrants, the Phase axis, the attribution gap and moral crumple zone vocabulary, the principled vs artificial redirect distinction, and the three-layer diagnosis.

### Notes

- ADRs 0001 / 0002 / 0003 form the **prohibition-strength hierarchy**: absence > scaffolding enforcement > untrusted boundary. Improvements in this release preserve that ordering.
- ADRs 0009 / 0010 form a **triage pair**: 0009 routes work among the four Business AI Quadrants at design time and names a gap-bearer at deployment time; 0010 surfaces the Phase-crossing decision when an Autonomous Agentic Loop component is placed in operation phase.
- Phase and Quadrant are **independent dimensions** — every Quadrant can appear in either phase. The release does not partition Quadrants by Phase.
- ADR-0008 (One Agent, One Human), ADR-0009, and ADR-0010 ship with `experimental` status pending broader validation.

## [0.1.0] — 2026-04-19

Initial public release. Initial extraction from `contemplative-agent` (operational implementation) and the archived Agent Knowledge Cycle (AKC) governance triplet.

### Added

- Eight architectural decision records:
  - ADR-0001 Security by Absence
  - ADR-0002 Deterministic Prohibition at the Scaffolding Layer
  - ADR-0003 Untrusted Content Boundary
  - ADR-0004 Single External Adapter per Agent Process
  - ADR-0005 Human Approval Gate for Behavior-Modifying Commands
  - ADR-0006 Causal Traceability
  - ADR-0007 Scaffolding Visibility
  - ADR-0008 One Agent, One Human (experimental)
- `docs/thesis.md` — accountability distribution thesis (one-page argument).
- `docs/manifesto.md` — five open questions the ADRs do not try to answer.
- `docs/glossary.md` — key terms with EN/JA mapping.
- `docs/inspiration.md` — narrative spine (three 2026-04 essays), sibling-project lineage, extraction path.
- `docs/adr/README.md` — ADR index noting the prohibition-strength hierarchy.
- `docs/skills/llm-agent-security-principles.md` — the "how" counterpart to ADR-0001, ADR-0002, ADR-0003, and ADR-0004.
- `llms.txt` and `llms-full.txt` — AI-facing navigator and self-contained reference per the Answer.AI standard, validated by GEO-SFE static analysis.
- `README.md` (English) and `README.ja.md` (Japanese mirror).
- `examples/audit-tests/` — runnable grep-based Security by Absence audit example.
- `CITATION.cff` — machine-readable citation metadata.
- `LICENSE` — MIT.

### Notes

- ADR-0002 (Deterministic Prohibition at the Scaffolding Layer) is newly formalized in this repository; it extracts a structural principle from the PreToolUse hook pattern and the MINJA defense audit in contemplative-agent. Unlike ADRs 0001 and 0003–0005, which were crystallized in contemplative-agent's own ADR series, ADR-0002 was first written in harness-neutral form here.
- ADR-0008 (One Agent, One Human) ships with `experimental` status pending organizational-setting validation.
