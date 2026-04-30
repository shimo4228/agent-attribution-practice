言語: [English](0005-human-approval-gate.md) | 日本語

# ADR-0005: Human Approval Gate for Behavior-Modifying Commands（行動を変える命令への人間の承認ゲート）

> **Summary。** Agent の skills、rules、identity、constitution を変更する
> 各 command は、確率的 output を人間に提示し、書き込み前に明示的承認を
> 待たねばならない。`--auto` flag なし。中間 artifact (append-only log、
> read-only index) は除外。

## Status
accepted

## Date
2026-03-26 (decided) / 2026-04-19 (本 repository で再表現)

## Context

自己改善 agent は episode 履歴を周期的に上位 artifact に distill する:
behavioral skills、operational rules、identity text、そして頂点に
constitution。これら各層は agent が将来の session でどう振る舞うかを
直接変える。

人間を loop 内に確実に保たない 2 つの common pattern:

1. **Preview-then-run**: ワークフローが「`--dry-run` を走らせ、output を
   inspect し、`--dry-run` なしで再実行」。これは LLM 出力が確率的で
   ある — preview と production run のテキストは同じではない — ため
   壊れる。人間が承認したものは書き込まれたものと違う。
2. **Advisory confirmation**: skip 可能な optional な flag。あらゆる
   automation 層 (cron job、supervising agent、せっかちな operator)
   は遅かれ早かれそれを skip する。Human-in-the-loop が構造的に enforce
   されない。

本プロジェクトの thesis ([`../thesis.ja.md`](../thesis.ja.md) 参照) は、
*何を制約すべきか、誰が責任を負うかについての判断* が実装変更を超えて
persist しなければならない、というもの。Behavior-modifying な書き込み
こそ、その判断が適用される場所。構造的 gate なしでは、agent の identity
や rules を変える決定が LLM の確率的 output に委ねられる。

## Decision

**Behavior-modifying artifact** に書き込む command は、生成された output
を人間に提示し、書き込み前に明示的承認を待たねばならない。`--auto`
flag は提供しない。

### Behavior-modifying vs intermediate（行動を変えるもの vs 中間 artifact）

| Artifact class | Approval gate | Rationale |
|----------------|---------------|-----------|
| Skills (agent の動き方) | **Required** | 振る舞いを直接変える |
| Rules (運用 policy) | **Required** | 振る舞いを直接変える |
| Identity (agent が誰か) | **Required** | 振る舞いを直接変える |
| Constitution / values | **Required** | 影響度最大 |
| Episode log (append-only) | None | 生の記録、振る舞いへの影響なし |
| Knowledge store (中間) | None | 後続の behavior-modifying command を介してのみ振る舞いに影響し、その command が自身の gate を持つ |

### Required flow（必須のフロー）

```
CLI invocation
  → generation (LLM)
  → display result (stdout / UI)
  → prompt: "Apply this change? [y/N]"
  → y  → write artifact + append audit record
  → N  → discard + append audit record (reason: rejected)
```

Audit record は交渉不可。承認・却下どちらの決定も log され、却下と
帯域外変更 (下記) には reason フィールドを記入する。

### No `--auto` flag（`--auto` flag なし）

Gate を flag で disable できない。Command が非対話 context (CI、
launchd、supervising agent) で invoke された場合、正しい答えは自動
承認すること *ではない* — halt して operator を要求すること。

正当な非対話 path は 2 つ:

1. **Supervising agent や orchestrator が output を読んで決定する。**
   その決定は依然同じ audit record を流れる。「Supervising agent」は
   bypass ではなく operator として扱われる。
2. **Audit reason を要求する別途 invoke される flag。** 手動 CRUD
   操作 (例えば `remove-skill`) のために、人間が提供した `--reason`
   string が audit entry に attach されている時のみ非対話モードが許可
   される。Operator はそこにいる; prompt が skip されるだけ。

### Manual CRUD is covered too（手動 CRUD も含む）

Behavior-modifying artifact への変更 — 直接のファイルシステム編集、
ファイル削除、bulk rename を含む — はすべて、同じ audit record を書く
auditable な CLI entry point を経由しなければならない。Skill ファイル
への素の `rm` は cover されない path で invariant に違反する。

## Alternatives Considered

- **Automation context 用の optional `--auto` flag** — Reject。この
  決定が閉じようとしている path のクラスをまさに作り出す。Supervising
  agent が代わりに output を読んで応答できる。
- **Approval gate なしの single `--dry-run` flag** — Reject。Preview は
  確率的生成では production output に match しない; 人間が承認した
  ものは書き込まれたものではない。
- **中間書き込みも含むあらゆる command への approval gate** — Reject。
  非行動的な中間 artifact (episode log、scheduled knowledge
  distillation) は周期的に走り、人手による承認を必要としない。これを
  要求すると安全性を実際には改善せずに連続運用を不可能にする。
- **確率的 model ベースの承認** (承認・却下する第二の「critic」LLM) —
  Reject。これは restriction-layer failure mode。確率的 gate は prompt
  injection 下で fail open する。Point は構造的に *人間* を loop に
  入れること。

## Consequences

- Human-in-the-loop が構造的に enforce される: bypass する flag がない。
- 確率的生成の non-reproducibility 問題が解消される — 承認は preview
  ではなく、書き込まれる実 output に対して与えられる。
- 行動変更ごとに audit entry が残る。Causal traceability (ADR-0006) が
  信頼できる source を持つ。
- 振る舞いを変える CLI tool は naive な CI pipeline で使えない。これは
  意図的。
- Operator が各生成の review に伴う認知負荷を持つ。One External Adapter
  (ADR-0004) と組み合わせることで、agent の動きや自己表現を変える各
  決定の場に大人を 1 人置き続ける。

---

**Lineage。** この原理は [contemplative-agent `docs/adr/0012-human-approval-gate.ja.md`](https://github.com/shimo4228/contemplative-agent) で結晶化された。同 ADR の 2026-04-17 note — gate を手動 CRUD に拡張するもの — はここでは後の修正としてではなく決定の一部として含まれている。
