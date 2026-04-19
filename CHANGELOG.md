# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
