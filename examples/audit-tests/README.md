# Security by Absence — runnable audit

A CI-compatible audit test for [ADR-0001 Security by Absence](../../docs/adr/0001-security-by-absence.md). If `grep` returns any lines outside a reviewed adapter directory, the audit fails.

## Quick check

From the repository root, run:

```bash
git grep -nE "subprocess|os\.system|\beval\(|\bexec\(|urlopen|requests\.(get|post|put|delete|patch)" \
  -- ':!docs' ':!examples' ':!tests'
```

Expected result on this repository: **zero hits**. The repository contains no executable agent code, only specifications.

## CI form

For a project that does contain agent code, run the grep and fail the build if anything outside a reviewed adapter returns. Example `make` target:

```make
audit-absence:
	@out=$$(git grep -nE "subprocess|os\.system|\beval\(|\bexec\(|urlopen|requests\.(get|post|put|delete|patch)" \
	  -- ':!docs' ':!examples' ':!tests' ':!src/adapters'); \
	if [ -n "$$out" ]; then \
	  echo "Security by Absence violation:"; \
	  echo "$$out"; \
	  exit 1; \
	fi
```

Tune the exclusion list (`:!docs`, `:!examples`, `:!tests`, `:!src/adapters`) to match the project. The rule is: any pattern match outside explicitly reviewed locations means the absence guarantee has been broken and a new ADR must be written before the build passes.

## Related

- [ADR-0001 Security by Absence](../../docs/adr/0001-security-by-absence.md) — the full principle and audit rationale.
- [ADR-0004 Single External Adapter](../../docs/adr/0004-single-external-adapter.md) — reviewed adapters are the only permitted location for external side effects.
