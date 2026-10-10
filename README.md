Language: English | [日本語](README.ja.md)

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652013.svg)](https://doi.org/10.5281/zenodo.19652013) [![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/agent-attribution-practice)

> **Agent Attribution Practice (AAP)**: ten judgments on what to constrain in an autonomous AI agent and who is responsible for it, paired with four Business AI Quadrants that decide whether the work needs an agent at all. Not a fixed framework.

Ten recurring judgments about how **attribution** (who authored the
behavior, who bears its consequences, who can reconstruct its cause)
should be distributed across an autonomous AI agent, paired with the
four-quadrant diagnostic frame that routes a piece of work to the
architecture preserving that distribution. Discovered by one operator
implementing and operating
[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)
and writing a seven-essay series, not prescribed top-down. For
architects, operators, and governance readers deciding whether, and
how, to put an autonomous agent into a given use case.

Everything here is documentation to read: architecture decision records
(ADRs), a thesis, and an adoption navigator, in Markdown, with nothing in
this repository to install. The adoption navigator, the security judgments and the
observability patterns also ship as installable Agent Skills, listed with
the author's other work under [More from the author](#more-from-the-author).

## Why this repository exists

AI governance, as the April 2026 essay that opened the series argues
([A Sign on a Climbable Wall](https://github.com/shimo4228/zenn-content/blob/main/articles-en/ai-agent-accountability-wall-en.md)),
is in its signpost phase: "don't do X" in system prompts, ethics
guidelines as PDFs, safety committees. Text without enforcement is a
climbable wall with a sign on it. What works, historically, is
accountability distributed through structure: capabilities that do
not exist cannot be invoked, blast radius is bounded by design, every
behavior change requires a named human's sign-off, every incident is
reconstructible after the fact. The thesis of this repository,
*accountability distribution*, is that the same holds for agents:
responsibility is spread across a chain (review, audit logs, approval,
postmortems) instead of resting on one actor. The ten ADRs here record
judgments of the form *what should be constrained, and who is
responsible*.

## The ten judgments

| ADR | Principle | Status |
|-----|-----------|--------|
| [0001](docs/adr/0001-security-by-absence.md) | Security by Absence — dangerous capabilities are never implemented, not restricted | accepted |
| [0002](docs/adr/0002-deterministic-prohibition-at-scaffolding.md) | Deterministic Prohibition at the Scaffolding Layer — when absence is unachievable, prohibit in the harness (the scaffolding code around the model), not in model weights | accepted |
| [0003](docs/adr/0003-untrusted-content-boundary.md) | Untrusted Content Boundary — accumulated memory cannot grant authority | accepted |
| [0004](docs/adr/0004-single-external-adapter.md) | Single External Adapter per Agent Process — blast radius bounded by design | accepted |
| [0005](docs/adr/0005-human-approval-gate.md) | Human Approval Gate — behavior-modifying writes require named human sign-off | accepted |
| [0006](docs/adr/0006-causal-traceability.md) | Causal Traceability — every event reconstructible after the fact | accepted |
| [0007](docs/adr/0007-scaffolding-visibility.md) | Scaffolding Visibility — behavior lives in files, not opaque weights | accepted |
| [0008](docs/adr/0008-one-agent-one-human.md) | One Agent, One Human — the accountability chain terminates at a named individual | **experimental** |
| [0009](docs/adr/0009-triage-before-autonomy.md) | Triage Before Autonomy — adopting an autonomous-loop architecture commits the system to a non-removable **attribution gap** (model, tool and context contributions that can no longer be separated after the fact) | **experimental** |
| [0010](docs/adr/0010-phase-separation.md) | Phase Separation — placing an autonomous loop in the operation phase (production, not design) requires a recorded decision to cross that boundary | **experimental** |

The first three form a **prohibition-strength hierarchy** (absence >
scaffolding enforcement > untrusted boundary); 0004 and 0005 add
topology and human-in-the-loop; 0006 and 0007 are the artifacts those
constraints require; 0008 is the human endpoint. ADRs 0009 and 0010
are the two triage steps: 0009 asks whether the work needs an
autonomous loop at all, and 0010 asks which phase (design or
operation) it may run in.

ADR-0001 gets a first-pass audit from one command. The patterns catch
Python-style process, eval and HTTP calls; they also match harmless
names such as `model.eval()` and miss calls written in other languages,
so adapt the patterns as well as the exclusions to your stack. In a
clean codebase the hits that remain are zero, or only inside an
adapter that an ADR explicitly allows:

```bash
git grep -nE "subprocess|os\.system|\beval\(|\bexec\(|urlopen|requests\.(get|post|put|delete|patch)" \
  -- ':!docs' ':!examples' ':!tests'
```

## The four Business AI Quadrants

The quadrants sort work by two questions: can it be written as
deterministic rules, or does it need semantic judgment from an LLM; and
is its control flow pre-defined in code, or must the next step be
decided at runtime (exploratory)?

|  | Pre-defined workflow | Exploratory |
|---|---|---|
| **Deterministic** | (1) Script Quadrant | (2) Algorithmic Search Quadrant |
| **Semantic-judgment** | (3) LLM Workflow Quadrant | (4) Autonomous Agentic Loop Quadrant |

As the essay that introduced the quadrants argues
([Where ReAct Agents Are Actually Needed in Business](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do),
2026-04-29), most current LLM applications belong to the **LLM Workflow Quadrant**
(deterministic control flow + bounded LLM calls with named roles), not
the **Autonomous Agentic Loop Quadrant** (the LLM decides each next step
at runtime). Routing the former into the latter is the structural
source of much of the accountability collapse that the author's essays
diagnose (see [Essays and papers](#essays-and-papers)). An autonomous
loop carries the attribution gap described under ADR-0009. Running one
without a pre-named **gap-bearer**, a party named before deployment who
absorbs that gap, is the failure mode ADR-0009 prevents. The ADRs answer per-question (*what should be
constrained, who is responsible*); the quadrants route the work to
where those answers apply. The two are separate axes, with Phase
(whether the work runs during design or in production operation) as an
independent third dimension.

## Using this as an agent-adoption navigator

This repository is meant to be *walked*, not only read. If you are
deciding whether to put an autonomous agent into a given use case — or
auditing one you already shipped — clone it, point your coding agent
at `AGENTS.md`, and use it as a sounding board:

1. [`docs/quadrants/decision-tree.md`](docs/quadrants/decision-tree.md) — five-question triage routing the work to a Quadrant
2. [`docs/quadrants/governance-mapping.md`](docs/quadrants/governance-mapping.md) — governance requirements for that Quadrant
3. The relevant ADRs, especially the two triage ADRs: 0009 (does the work need an autonomous loop?) and 0010 (design or operation phase?)
4. [`docs/quadrants/anti-patterns.md`](docs/quadrants/anti-patterns.md) — final check against known failure modes

*Worked example (illustrative):* an
autonomous refund-approval loop routes to the Autonomous Agentic Loop
Quadrant at step 1; step 3 then makes all ten ADRs load-bearing,
including ADR-0009's pre-named gap-bearer before the loop goes live.
More routings, each dated and also illustrative rather than endorsed
reference architectures (a coding agent, FAQ classification, invoice matching,
contract review and others), are in
[`docs/quadrants/case-studies.md`](docs/quadrants/case-studies.md).

The ADRs are a starting point for judgment, not a verdict —
re-interpret them against your own context.

## Essays and papers

The argument was developed across seven essays published April–May
2026: a trilogy (problem statement → post-incident causal tracing →
two-layer black-box analysis) plus four architectural follow-ups
(quadrant triage → vocabulary diagnosis → phase distinction →
skill-design gradient). Links, dates, per-essay summaries and the
lineage of each ADR are in [`docs/inspiration.md`](docs/inspiration.md).

Two companion position papers distil the essays into harness-neutral
statements (open access, CC BY 4.0; the concept DOIs always resolve to
the latest version):

- Shimomoto, T. (2026). *Distributing Accountability, Not Capability: Phase Separation and the LLM Workflow Quadrant in Autonomous AI Agent Architectures* (essays 4–7). Zenodo. [doi:10.5281/zenodo.20353789](https://doi.org/10.5281/zenodo.20353789) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6817598)
- Shimomoto, T. (2026). *The Two-Layer Black Box: Operator Visibility, Commercial Secrecy, and a Minimum Disclosure Set for Accountable Autonomous AI Agents* (essays 1–3). Zenodo. [doi:10.5281/zenodo.20355907](https://doi.org/10.5281/zenodo.20355907) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6823878)

## External mappings

Two time-bound readings are kept out of the ADRs so that the ADRs stay
vendor- and framework-neutral.
[`docs/industry-mapping.md`](docs/industry-mapping.md) maps 2026
industry releases (policy gates, agent-identity primitives, sponsor
systems, cross-vendor audit) to the judgments they leave open.
[`docs/policy-mapping/`](docs/policy-mapping/README.md) maps the ADRs
and Quadrants to NIST AI RMF 1.0 (with the Generative AI Profile),
ISO/IEC 42001:2023, the EU AI Act, and Singapore's Model AI Governance
Framework for Agentic AI; it is a reading and a citation surface, not a
compliance attestation.

## Reading order

1. [`docs/thesis.md`](docs/thesis.md) — the argument for accountability distribution
2. [`docs/glossary.md`](docs/glossary.md) — term definitions (accountability distribution, attribution gap, the four Quadrants, Phase)
3. [`docs/adr/README.md`](docs/adr/README.md) — index of ADRs
4. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) — the cleanest entry; its audit test is the command shown above
5. The seven essays in publication order (listed in [`docs/inspiration.md`](docs/inspiration.md))
6. [`docs/quadrants/`](docs/quadrants/) — adoption navigator: decision tree, governance mapping, case studies, anti-patterns
7. [`docs/manifesto.md`](docs/manifesto.md) — civilization-scale questions the ADRs do not attempt to answer
8. [`docs/social-consequence.md`](docs/social-consequence.md) — a companion essay outside the seven, on what happens in society when accountability is not distributed; kept separate from the ADRs

**Japanese readers:** see [`README.ja.md`](README.ja.md). The ADRs,
thesis, glossary, quadrants navigator, industry mapping, policy
mappings and social-consequence essay ship `.ja.md` mirrors; `docs/manifesto.md` and
`docs/inspiration.md` are English only.

## What this repository does not claim

- That these ten are complete.
- That the specific implementations they were extracted from are
  durable. Implementation dissolves; judgment persists.
- That these principles solve the larger questions of AI direction,
  labor redesign, or social consent. Those remain open — see
  [`docs/manifesto.md`](docs/manifesto.md).
- That top-down AI governance policy is wrong. It is a different
  layer, with a different method. This repository is about what
  emerges from the bottom — one operator, one agent, and the friction
  of running it.

## Origin

First compiled by Tatsuya Shimomoto
([@shimo4228](https://github.com/shimo4228),
[ORCID 0009-0002-6168-4162](https://orcid.org/0009-0002-6168-4162))
in April 2026. AAP's sibling
[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle)
distils the same implementation friction into mechanism patterns (how
knowledge flows), where AAP records how attribution is distributed.

## How to Cite

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.21218784},
  url          = {https://doi.org/10.5281/zenodo.21218784},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (three experimental), paired with four Business AI Quadrants as the diagnostic frame in a two-axis structure, with Phase as an independent third dimension}
}
```

Or in text:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.21218784

The badge at the top carries the **concept DOI**
([10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013),
always resolving to the latest version); the BibTeX above pins the
**version DOI** of the current release.

## More from the author

- **[Where ReAct Agents Are Actually Needed in Business](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do)** ([日本語](https://zenn.dev/shimo4228/articles/react-agent-business-quadrant)): the full argument, summarized above, for why most LLM applications belong in the LLM Workflow Quadrant rather than an autonomous loop.
- **[Why Did My Agent Decide That? 3 Observability Patterns](https://dev.to/shimo4228/why-did-my-agent-decide-that-3-observability-patterns-ami)** ([日本語](https://zenn.dev/shimo4228/articles/agent-observability-patterns)): the observability patterns behind ADR-0006 and ADR-0005, shown on the running agent they came from.
- **[agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage)**: five questions that route AI work to a script, algorithmic search, an LLM workflow or an autonomous agent before anything is built; the installable form of the quadrants navigator and ADR-0009 / ADR-0010.
- **[llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles)**: structural security for autonomous agents (remove the capability instead of guarding it, treat accumulated content as untrusted, one external adapter per agent process); the installable form of ADR-0001 to ADR-0004.
- **[agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns)**: three skills (replayable audit logs, read-only instruments, shadow-mode validation) for observing an agent before you intervene; the installable form of ADR-0006 and ADR-0005.
- **[shimo4228](https://github.com/shimo4228/shimo4228)**: the author's hub, with AAP next to the other long-running practice lines and their DOIs.

## License

MIT

<details>
<summary>For tools and AI assistants</summary>

Agent Attribution Practice (AAP) is a research repository of ten architecture decision records (ADRs) and a four-quadrant diagnostic frame that records how attribution (who authored an agent's behavior, who bears its consequences, who can reconstruct its cause) should be distributed across an autonomous AI agent, for architects, operators and governance readers deciding whether and how to deploy one.

It exists because most current AI governance is text without enforcement (instructions in system prompts, guideline PDFs, safety committees), while what has worked historically is accountability distributed through structure: capabilities that do not exist cannot be invoked, blast radius is bounded by design, every behavior change needs a named human's sign-off, and every incident can be reconstructed afterwards. The judgments surfaced through implementing and operating the author's contemplative-agent and through a seven-essay series, and were re-expressed so that the ADRs name no harness, vendor product or governance framework; vendor and framework readings live in separate, time-bound mapping documents.

Canonical facts: MIT license; no code to run (Markdown documents plus machine-readable metadata: graph.jsonld, llms.txt, llms-full.txt, CITATION.cff, codemeta.json); English primary, with Japanese mirrors (`*.ja.md`) of the README, ADRs, thesis, glossary, quadrants navigator, the industry-mapping file, the policy-mapping directory and the social-consequence essay; compiled by Tatsuya Shimomoto (@shimo4228, ORCID 0009-0002-6168-4162) from April 2026. Status: active, curated by hand by the author; ADR-0001 to ADR-0007 are accepted and ADR-0008 to ADR-0010 are marked experimental. Requirements: none to read it; to use it as a navigator, clone it and point a coding agent at `AGENTS.md`. The concept DOI [10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013) always resolves to the latest version; cite with the BibTeX under How to Cite or with `CITATION.cff`.

Core concepts. *Accountability distribution*: spreading responsibility across a chain (review, audit logs, approval, postmortems) instead of concentrating it in one actor; the thesis is that this applies to agents too. *Prohibition-strength hierarchy*: absence (ADR-0001) > scaffolding enforcement (ADR-0002) > untrusted boundary (ADR-0003); a prohibition drops to the next layer only when the current one cannot hold the capability. *Business AI Quadrants*: deterministic vs semantic-judgment work crossed with pre-defined vs exploratory control flow, giving the Script, Algorithmic Search, LLM Workflow and Autonomous Agentic Loop Quadrants. *Attribution gap*: when an architecture blends model output, tool choice, history and prompt context at runtime, the contributions can no longer be separated after the fact; the gap is intrinsic to the Autonomous Agentic Loop Quadrant. *Gap-bearer*: the party named before deployment who absorbs that gap (ADR-0009). *Phase*: design vs operation, a dimension independent of Quadrant; placing an autonomous loop in the operation phase requires a recorded Phase-crossing decision (ADR-0010).

Example (illustrative): an autonomous refund-approval loop routes to the Autonomous Agentic Loop Quadrant at the first step of the decision tree; all ten ADRs then become load-bearing, including ADR-0009's requirement to name a gap-bearer before the loop goes live (more routings, also illustrative, are in `docs/quadrants/case-studies.md`).

Links: [graph.jsonld](graph.jsonld) is the machine-readable relationship map (Quadrants, ADRs, prohibition levels, Phase); [llms.txt](llms.txt) is the compact index and [llms-full.txt](llms-full.txt) the consolidated reference; [docs/adr/](docs/adr/README.md), [docs/quadrants/](docs/quadrants/README.md) and [docs/glossary.md](docs/glossary.md) hold the records, the navigator and the definitions. The installable skills are [agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage) (ADR-0009 and ADR-0010), [llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles) (ADR-0001 to ADR-0004) and [agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns) (ADR-0006 and ADR-0005). The author's whole ecosystem is mapped in the hub's [graph.jsonld](https://github.com/shimo4228/shimo4228/blob/main/graph.jsonld).

</details>
