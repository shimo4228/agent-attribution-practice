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

ADR-0001 / 0002 / 0003 は「**禁止強度の階層**」を成す: absence > scaffolding enforcement > untrusted boundary。改訂時にこの順序関係を壊さない。

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

## 3 記事の narrative spine

この repo の argument は zenn 記事 3 部作として展開された。ADR 改訂・新規追加の際、この spine との整合を必ず確認する:

1. [登れる壁に看板を立てても意味がない](https://zenn.dev/shimo4228/articles/ai-agent-accountability-wall) (2026-04-06) — 問題提起
2. [事故のあとで因果を辿れるか](https://zenn.dev/shimo4228/articles/agent-causal-traceability-org-adoption) (2026-04-13) — 応用
3. [AIエージェントのブラックボックスは二層ある](https://zenn.dev/shimo4228/articles/agent-blackbox-capitalism-timescale) (2026-04-14) — 障害分析

記事本文 `/Users/shimomoto_tatsuya/MyAI_Lab/zenn-content/articles/` にローカルコピーあり。

## Core thesis

> 「実装は溶ける、判断は残る」
> 「能力の分配ではなく、**アカウンタビリティの分配**」

詳細は [`docs/thesis.md`](docs/thesis.md)。

## 書き方の規約

### ADR の harness-neutral 化

本 repo の ADR は project / harness 固有の識別子を含めない:

- ❌ `Moltbook adapter`, `contemplative-agent の insight CLI`, `MOLTBOOK_HOME`
- ✅ `a social platform adapter`, `a skill-generating command`, `the agent's data directory`

実装具体例を挙げる場合は「Lineage」セクション末尾に `contemplative-agent` への link で対応。ADR 本文の決定・理由・代替案は harness-neutral に保つ。

### 英語 primary / 日本語 subordinate

- README.md は英語正本、README.ja.md が日本語
- ADR は英語のみ (contemplative-agent は ja 版もあるが、本 repo は簡素化)
- manifesto / thesis は英語正本。日本語版は v0.2.0 以降の判断

### ADR format

- 必須 section: Status / Date / Context / Decision / Alternatives Considered / Consequences
- Lineage section を末尾に添える (どこから来た判断か)
- experimental status を明示する場合は Status 欄に `**experimental**` と太字で付記

### 「判断」と「実装」の区別

ADR 本文が扱うのは *judgment* (persistent)。*implementation* (Hooks、CLI、JSONL スキーマ等) は Lineage セクションや外部 link で触れる。「実装は溶ける、判断は残る」という thesis を本文構造で体現する。

## Commit / release 方針

- Commit は user の明示的な指示時のみ (global 規約を踏襲)
- v0.1.0 release 時に CITATION.cff + Zenodo DOI を発行
- CHANGELOG.md は v0.1.0 時点で起稿

## ディレクトリ

```
├── LICENSE (MIT)
├── README.md / README.ja.md
├── CLAUDE.md              # 本ファイル (Claude Code 用 context)
├── .notes/                # WIP (gitignored)
├── docs/
│   ├── thesis.md          # accountability distribution thesis
│   ├── manifesto.md       # civilization-scale open questions (draft)
│   ├── glossary.md        # key terms (dissolution 健全性 5 軸は provisional)
│   ├── inspiration.md     # 3 記事 + moltbook + AKC 系譜
│   ├── adr/
│   │   ├── README.md
│   │   └── 0001-0008.md   # 8 本の ADR (0001/0002/0003 は prohibition-strength hierarchy)
│   └── skills/
│       └── llm-agent-security-principles.md
└── examples/
    └── audit-tests/       # grep-based Security by Absence 検査の CI 化例
```

## 残議題

cold-start 用の context は [`.notes/remaining-discussion-2026-04-19.md`](.notes/remaining-discussion-2026-04-19.md) を読む (gitignored、clone 先に存在しないため、同一環境での作業継続用)。
