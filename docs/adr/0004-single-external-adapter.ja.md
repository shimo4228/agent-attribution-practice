言語: [English](0004-single-external-adapter.md) | 日本語

# ADR-0004: Single External Adapter per Agent Process（Agent process あたり 1 つの External Adapter）

> **Summary。** 単一の agent process は外部 adapter を最大 1 つ持つ、
> 1 つの destination に狭い interface で pin されたもの。複数の外部
> surface は複数の process に分割しなければならない、各々が自身の log、
> 自身の knowledge、自身の adapter を持つ。

## Status
accepted

## Date
2026-04-09 (decided) / 2026-04-19 (本 repository で再表現)

## Context

Agent が外の世界に影響する手段を 2 つ以上持った瞬間 — social platform
への post、email 送信、billing API の呼び出し、共有 drive への書き
込み — どんな失敗や prompt-injection exploit も、その blast radius は
agent が到達できる全てのものの和集合になる。さらに悪いことに、「どの
side effect がどの input によって起きたか」のデバッグが combinatorial
になる。

Multi-capability agent はまた、適切な瞬間に適切な tool を選ぶことを
agent の判断に暗黙に依存する。その判断こそ、agent を狙う攻撃者が
影響を与えたい対象である。

## Decision

単一の agent process は **最大 1 つの external adapter** を持ちうる
— 「external」とは「agent の local data directory の外で観察可能な
side effect を生み出せる」を意味する (outbound HTTP writes、金融
transaction、メッセージ、data directory 外のファイル)。

複数の external surface が本当に必要な use case なら、複数の agent
process に分割する — 各々が 1 つの adapter と自身の episode log を
持ち、互いの log を通じて通信する、共有 runtime state を通じてではなく。

### What counts as "external"（何が「外部」と数えるか）

| Adapter type | External? |
|--------------|-----------|
| 特定 domain への HTTP POST | Yes |
| 読み取り専用データの HTTP GET | Yes、ただし lower risk (それでも数える) |
| LLM 推論 (local か remote) | **No** — pure function として扱う |
| Agent の data directory 内への書き込み | No |
| Data directory 外への書き込み | Yes |
| Subprocess 実行 | ADR-0001 で全面禁止 |

### What counts as "one"（何が「1 つ」と数えるか）

「1 つの adapter」とは、1 つの pin された destination に対する狭い
interface のこと。`www.moltbook.com` だけと話す Moltbook adapter、1 つの
workspace だけと話す Slack adapter、1 つの instance だけと話す
Mastodon adapter — 各々が「1 つ」。汎用 HTTP client は「1 つ」ではない。

### Composition by process, not by capability（capability ではなく process で構成する）

複数の external surface を持つ system を build するには、複数の agent
process を動かす。各々が自身の episode log、自身の knowledge store、
自身の identity、自身の adapter を持つ。Process 間の調整は、1 つが
他方の published output を読むことで起きる — 決して in-process の
共有 state を通じてではない。

## Alternatives Considered

- **Per-adapter permission を持つ multi-adapter agent** — 制限ベースの
  security と等価 (ADR-0001 がこれを reject する)。Capability の和集合
  問題を取り除かずに保守すべきコードを足す。
- **Capability router を持つ shared runtime** — Blast radius を router
  に移すだけ。Router は単一の point になり、それが compromise されると
  全 capability を grant する。
- **広い capability を持つ 1 つの monolithic agent** — 運用上は単純、
  アーキテクチャ上は脆弱。Reject。

## Consequences

- System のスケールは process を追加することで起きる、1 つの process に
  capability を追加することではなく。
- デバッグが clean: 各 log が 1 つの adapter の side effect に対応する。
- Process 間の調整は非同期で reviewable (ある agent が別の agent の
  published state を読む)、これは ADR-0005 の Approval Gate 原則と
  match する。
- Adapter は domain pinning、redirect block、request サイズ制限を
  enforce する自然な場所になる。

---

**Lineage。** この原理は最初に [contemplative-agent `docs/adr/0015-one-external-adapter-per-agent.ja.md`](https://github.com/shimo4228/contemplative-agent) として登場し、[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) v1.x で generalized され、2026-04-19 に本 repository に抽出された。
