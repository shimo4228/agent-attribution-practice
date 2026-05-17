# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
