<!-- Generated: 2026-05-17 | Total codemaps: 1 | Token estimate: ~260 -->
# Codemaps Index

Architectural documentation for **agent-attribution-practice (AAP)** — a DOI-registered judgment-artifact repository (10 ADRs + 4 Business AI Quadrants + Phase axis, English primary with Japanese mirror).
**Last Updated**: 2026-05-17 | **Repo**: 62 markdown / text files, no source code (judgment-artifact repo)

---

## Quick Navigation

### 1. [architecture.md](architecture.md) — Document Architecture

**Read first.** Top-level layout, document-role index, two-axis + Phase structure, citation-dependency graph, English / Japanese pairing rule, sibling-repo relationships.

**Topics**:
- Top-level layout (`docs/`, `docs/adr/`, `docs/quadrants/`, `docs/CODEMAPS/`, `docs/skills/`, `examples/`)
- Document roles (which document answers which question — for citation routing)
- Two-axis + Phase structure (10 ADRs × 4 Quadrants × Phase as independent third dimension)
- Prohibition-strength hierarchy (ADR-0001 / 0002 / 0003 ordering invariant)
- Triage pair (ADR-0009 / 0010 problem-space + time-axis)
- Citation-dependency graph (thesis → ADR-N → glossary → industry-mapping / policy-mapping triangulation paths)
- LLM-facing artifacts (llms.txt, llms-full.txt) and how they relate to source docs
- English-primary / Japanese-subordinate pairing convention
- Sibling-repo map (contemplative-agent / AKC / contemplative-agent-rules / contemplative-moltbook)
- File-count snapshot

**Use when**: Citing AAP from an LLM-mediated channel; deciding which document is canonical for a given question; navigating the ADR ↔ Quadrant ↔ glossary ↔ industry-mapping web; understanding what counts as judgment vs time-bound mapping.

---

## Maintenance

Regenerate when: (a) new ADR / Quadrant / top-level doc added, (b) sibling-repo relationships change, (c) `industry-mapping.md` or `policy-mapping/*` mappings shift substantially (e.g. NIST profile revision, ISO 42001 amendment, new framework file added). Stale codemaps lie about the structure — date-stamp them and treat them as decaying.
