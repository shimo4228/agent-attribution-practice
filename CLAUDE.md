# agent-attribution-practice

自律 AI エージェントにおける **attribution の分配** (誰が書いたか、誰が原因か、誰が事後に辿れるか) に関する 7 つの判断を記録する DOI-targeted research project。`contemplative-agent` の実装・運用の摩擦から発見されたものを、harness-neutral に再表現している。

> Project name: **Agent Attribution Practice (AAP)** (2026-04-19 確定)。repo directory・GitHub URL ともに `agent-attribution-practice` で統一。

## Sibling projects (context を失わない)

この repo は単独の artifact ではなく、3 つの sibling との関係で意味を持つ。

### `contemplative-agent` — 実装

- https://github.com/shimo4228/contemplative-agent
- 運用中の実装。この repo の各 ADR はそちらの対応 ADR を、project 固有の詳細を剥がして再表現したもの
- 対応表:

| 本 repo | contemplative-agent |
|---|---|
| ADR-0001 Security by Absence | ADR-0007 Security Boundary Model (partially) |
| ADR-0002 Deterministic Prohibition at Scaffolding | PreToolUse hooks (`block-episode-logs-{read,bash,grep}.sh`) + `summarize_record` quarantine; `docs/security/2026-04-01-episode-log-access-control.md`, ADR-0028 / ADR-0029 audit |
| ADR-0003 Untrusted Content Boundary | ADR-0007 Security Boundary Model (partially) |
| ADR-0004 Single External Adapter | ADR-0015 One External Adapter Per Agent |
| ADR-0005 Human Approval Gate | ADR-0012 Human Approval Gate |
| ADR-0006 Causal Traceability | (暗黙、essay で言語化) |
| ADR-0007 Scaffolding Visibility | (暗黙、essay で言語化) |
| ADR-0008 One Agent, One Human | (新規、本 repo で初形式化、experimental) |
| ADR-0009 Triage Before Autonomy | (新規、本 repo で初形式化、experimental — 5 部作 essay の 4-5 から派生) |
| ADR-0010 Phase Separation | (新規、本 repo で初形式化、experimental — 7 部作 essay の 6-7 から派生; 7 が Phase 軸を skill 設計粒度に descend) |

ADR-0001 / 0002 / 0003 は「**禁止強度の階層**」を成す: absence > scaffolding enforcement > untrusted boundary。改訂時にこの順序関係を壊さない。

ADR-0009 / ADR-0010 は triage pair: 0009 は ADR 群の **手前** に位置する problem-space triage、0010 は autonomy 採用時の Phase-crossing decision を operation 配置で要求する。10 ADRs と 4 Business AI Quadrants は **二軸構造** を成し、Phase は Quadrant と独立な第三の dimension (どの Quadrant も design / operation 両 phase に出現しうる)。`docs/quadrants/` 参照。

本 repo で ADR を改訂する時は、必要に応じて contemplative-agent 側の対応 ADR も確認する。逆方向 (contemplative-agent の ADR 改訂時に本 repo を更新) も同様。

### `agent-knowledge-cycle` (AKC) — mechanism sibling

- https://github.com/shimo4228/agent-knowledge-cycle (DOI `10.5281/zenodo.19200727`)
- v2.0.0 (2026-04-19) で mechanism-only に再ポジショニング
- それまで AKC v1.x にあった governance triplet (Security by Absence / Single External Adapter / Untrusted Content Boundary) が `_archive/akc-security-triplet-2026-04/` に凍結され、本 repo が sibling genre library として立ち上がった
- AKC は cycle (knowledge をどう流すか)、本 repo は practice (attribution をどう分配するか)。役割は相補的で独立

### `contemplative-agent-rules` — 別 sibling

- https://github.com/shimo4228/contemplative-agent-rules
- 四公理ルール、アダプタ、ベンチマーク
- content 面で重複領域が生じたら、双方の positioning を確認 (四公理 values は rules 側、attribution practice は本 repo)

## Using this repo as an agent navigator

この repo を Claude Code / Codex 等の agent に clone させ、`AGENTS.md` (= `CLAUDE.md` への symlink) を起点に「agent 活用判断の壁打ち相手」として使う使い方を想定する。文献として読まれるのではなく、navigator として walk されることを意図する。

### 想定 use case

- 「この用途で agent を入れていいか judgment したい」
- 「既に入れた agent の risk を ADR で照合したい」
- 「特定の autonomy パターンが anti-pattern に該当するか確認したい」

### Walk order (triage 依頼を受けたら)

1. [`docs/quadrants/decision-tree.md`](docs/quadrants/decision-tree.md) で 5 問 triage → 4 quadrant のどれに属するか決定
2. [`docs/quadrants/governance-mapping.md`](docs/quadrants/governance-mapping.md) で該当 quadrant の governance 要件を確認
3. 関連 ADR を読む — 特に triage pair (ADR-0009 / ADR-0010)。一覧と概要は [`docs/adr/README.md`](docs/adr/README.md) 参照
4. autonomy 関連なら **Phase 軸** (ADR-0010 Phase Separation) を別途確認 (design phase か operation phase か)
5. [`docs/quadrants/anti-patterns.md`](docs/quadrants/anti-patterns.md) で照合し、anti-pattern に該当しないか最終チェック

### Caveats

- **ADR は判断の出発点であって正解ではない**。user の context に応じて re-interpret する (Emptiness 条項相当の注意)
- **「実装は溶ける、判断は残る」** — agent が言語化する具体実装は時代依存、ADR の judgment 構造のみが時間軸で残る
- **Out of scope**: この repo は実装ガイドではない。実装 sibling は [`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)

## 7 記事の narrative spine

この repo の argument は zenn 記事 7 部作として展開された。最初の 3 つが trilogy (問題提起 / 応用 / 障害分析)、後の 4 つが architectural follow-up (4 象限 triage、principled attribution gap、設計 / 運用 phase の区別、skill-design gradient への phase descent)。ADR 改訂・新規追加の際、この spine との整合を必ず確認する。

ADR との lineage 対応:
- 記事 1-3 (trilogy, 2026-04-06 / 04-13 / 04-14) → ADRs 0001-0008 の問題意識・正当化の背景
- 記事 4-5 (2026-04-29 / 04-30) → ADR-0009 と `docs/quadrants/` navigator の lineage
- 記事 6-7 (2026-05-01 / 05-02) → ADR-0010 の lineage (記事 7 は Phase Separation を skill 設計粒度に下降)

各記事の URL・タイトル・1 文サマリは [`docs/inspiration.md`](docs/inspiration.md) を正本として参照。

## Core thesis

> 「実装は溶ける、判断は残る」
> 「能力の分配ではなく、**アカウンタビリティの分配**」

詳細は [`docs/thesis.md`](docs/thesis.md)。

## 書き方の規約

### ADR の harness / vendor / framework 中立性

本 repo の ADR は project / harness 固有の識別子、特定 vendor 製品名、特定 framework / 標準名のいずれも含めない:

- ❌ project / harness: `Moltbook adapter`, `contemplative-agent の insight CLI`, `MOLTBOOK_HOME`
- ❌ vendor 製品: `Microsoft Agent Governance Toolkit`, `Purview`, `Entra Agent ID`
- ❌ framework / standard: `NIST AI RMF`, `ISO/IEC 42001`, `GOVERN function`, `AI Management System`
- ✅ project / harness: `a social platform adapter`, `a skill-generating command`, `the agent's data directory`
- ✅ vendor / framework は ADR 本文では一般語で言及 (`a sub-millisecond policy gate`, `an AI management system`, `a national AI risk management framework`)

実装具体例 (vendor instance) は [`docs/industry-mapping.md`](docs/industry-mapping.md) に隔離する。framework 対応 (NIST / ISO / EU AI Act / OECD への mapping) は [`docs/policy-mapping/`](docs/policy-mapping/README.md) に隔離する。両 directory は time-bound で decay する; ADR 本文の決定・理由・代替案は harness / vendor / framework neutral に保ち、いかなる vendor 製品改廃や framework 改訂も ADR 本文に back-propagate させない。`contemplative-agent` への link は「Lineage」セクション末尾に。

### 英語 primary / 日本語 subordinate

- README.md は英語正本、README.ja.md が日本語
- ADR は英語正本 + 日本語 subordinate。各 ADR に `*.ja.md` ペアを併存させる (contemplative-agent と同じ方針、2026-05-01 から)
- manifesto は英語正本。日本語版は v0.2.0 以降の判断
- thesis / glossary / quadrants は英語正本 + 日本語 subordinate (`*.ja.md` ペア)

### ADR format

- 必須 section: Status / Date / Context / Decision / Alternatives Considered / Consequences
- Lineage section を末尾に添える (どこから来た判断か)
- experimental status を明示する場合は Status 欄に `**experimental**` と太字で付記

### 「判断」と「実装」の区別

ADR 本文が扱うのは *judgment* (persistent)。*implementation* (Hooks、CLI、JSONL スキーマ等) は Lineage セクションや外部 link で触れる。「実装は溶ける、判断は残る」という thesis を本文構造で体現する。

## ディレクトリ

repo の構造と各 doc の役割は [`docs/CODEMAPS/architecture.md`](docs/CODEMAPS/architecture.md) を参照 (canonical)。

[`graph.jsonld`](graph.jsonld) と [`docs/CODEMAPS/architecture.md`](docs/CODEMAPS/architecture.md) は同じ project を **異なる abstraction 層** で扱う:

- **CODEMAPS = file-level**: 「どのファイル / モジュールに X が住んでいるか」を prose で記述。人間 + agent が code を navigate する時に読む
- **graph.jsonld = concept-level**: 「X とは何か、X と Y はどう関係するか」を JSON-LD triples で encode。AI search engine + LLM が entity を citation する時に読む

両者は重複せず相補的。同じ entity を別角度から見る（例: `Quadrant` は CODEMAPS では `docs/quadrants/README.md` に住むファイル、graph.jsonld では `appliesTo` edges を持つ concept node）。新規 ADR / Quadrant / Concept 追加時は **両面で更新** する。役割境界の正本定義 + drift 防止規約は `~/.claude/skills/jsonld-knowledge-graph/SKILL.md` の "CODEMAPS との関係" セクション参照。
