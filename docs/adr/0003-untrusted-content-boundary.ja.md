言語: [English](0003-untrusted-content-boundary.md) | 日本語

# ADR-0003: Untrusted Content Boundary（信頼しない蓄積内容の境界）

> **Summary。** 蓄積された agent state すべて — episode log、knowledge
> store、distilled identity — は prompt に読み戻される時 untrusted
> content（信頼しない蓄積内容）として扱う、agent 自身が author した
> content も含めて。Distillation 出力は自身が source とした内容の汚染を
> 継承する。

## Status
accepted

## Date
2026-04-09 (decided) / 2026-04-19 (本 repository で再表現)

## Context

自己改善 agent のメモリは、agent が author していないテキスト —
user message、tool output、LLM 自身の hallucination、adapter から
fetch した content — によって直接・間接に populate される。Memory の
各行は将来の prompt に流れ戻る。

通常の脅威モデルは「user input」を untrusted、「agent-generated text」を
trusted と framing する。この framing は自己改善 agent には誤りである。
Agent 自身の distillation 出力は untrusted input の summary であり、
悪意ある content を確実に refuse する手段を持たない確率的 model で
処理されたもの。**Summary は source の汚染を継承する。**

## Decision

蓄積された agent state すべて — episode log、knowledge store、
identity — は prompt に読み戻される時 **untrusted content** として
扱う。

### Write-time rules（書き込み時の rule）

- 外部 input (adapter の response、user message、tool output) は
  episode log に逐語的に記録される。Write-time では構造的に必要な
  最低限 (有効な JSON) を超える sanitization をしない。
- 記録は atomic: 1 event 1 行、部分書き込みなし。

### Read-time rules（読み出し時の rule）

- Knowledge ファイルを load する時、content を accept する前に
  forbidden-substring list で validate する。Forbidden pattern が
  存在すれば fail closed する (ファイルを空として扱い、warning を log
  に書く) — fail open ではなく。
- 蓄積 content を prompt に注入する時、明示的な境界 marker (例えば
  `<untrusted_content>...</untrusted_content>`) で包む。Model が
  context のどの部分を自分で author したものでないかについて明確な
  signal を得るため。
- Distillation から identity や rules を生成する時、変更を accept
  する前に output を forbidden pattern で validate する。

### Forbidden substring list

List 自体は project-specific な関心事。本 repository は canonical
list を ship しない; operator は自身の脅威モデルに基づいて定義する。
[contemplative-agent `src/contemplative_agent/core/config.py`](https://github.com/shimo4228/contemplative-agent)
の実装が 1 つの形 (plain Python tuple を case-insensitively に check)
を示している。

### Why validation, not filtering（なぜ filtering ではなく validation か）

Filtering (「悪い部分を取り除く」) は tampering の証拠を隠し、下流
コードが部分的に sanitize された document を clean なものとして消費
することを許す。Validation (「悪い部分があれば reject」) は forensic
trail を保ち、人間がそのファイルを見ざるを得なくする。

## Alternatives Considered

- **Agent-authored memory を trust する** — Agent-authored text が
  untrusted input の関数であるという事実を無視する。安全ではない。
- **Read 時に filter する** — Tampering を隠し、partially-sanitized
  な state を生み出す。
- **Memory を encrypt して decrypt 後の形を trust する** — Encryption は
  confidentiality を保証するが、書き込みを行う entity に対する integrity
  は保証しない。書き手は agent *自身* なので、これは助けにならない。

## Consequences

- Forbidden-pattern validation は安価 (1 pass、case-insensitive) で、
  load のたびに実行できる。
- 汚染された knowledge ファイルは直ちに visible になる: warning log
  が特定のファイルを signal し、operator は episode log から store を
  reset するか判断する前にそれを inspect できる。
- Prompt injection は memory 経由で特権的経路を獲得しない、たとえ
  memory 自体が injected text を含んでいたとしても。

---

**Lineage。** この原理は最初に [contemplative-agent `docs/adr/0007-security-boundary-model.ja.md`](https://github.com/shimo4228/contemplative-agent) の read-time / write-time rule として登場し、[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) v1.x で独立 ADR に generalized され、2026-04-19 に本 repository に抽出された。
