言語: [English](governance-mapping.md) | 日本語

# Governance Mapping — Quadrants × ADRs × Controls

> **目的。** 各象限に適用される ADR、必要な controls、失敗時の
> redirect 経路を dense に reference できる形にしたもの。象限が確定
> した後に [`decision-tree.ja.md`](decision-tree.ja.md) と並べて使う。

## 一覧

| Quadrant | Governance level | 適用 ADR | 必要 controls | 失敗時の redirect 先 |
|---|---|---|---|---|
| (1) Script | Light (AAP の射程外) | — | code review, tests | code author / pipeline owner |
| (2) Algorithmic Search | Light (AAP の射程外) | — | algorithm validation, search-space audit | algorithm author |
| (3) LLM Workflow | Medium | 0001, 0003, 0004, 0005, 0006, 0007 | scaffolding visibility, audit log, behavior change の approval, schema 準拠の呼び出し | 失敗した LLM 呼び出しの role owner → routing-logic designer → upstream-data owner |
| (4) Autonomous Agentic Loop | High | **10 本の ADR すべて** | + pre-named gap-bearer（事前命名された責任引受者）(0008 + 0009)、behavior-modifying write への human approval (0005)、完全 causal trace (0006)、accountable operator 1 名 (0008)、**operation phase 配置時は Phase-crossing decision の記録 (0010) を要する** | pre-named gap-bearer (principled gap; 部品レベルの redirect は閉ざされる) |

### Phase 注記 (Quadrant とは独立)

Phase (design / operation) は Quadrant と独立 — framing は
[README — Phase は第 3 の dimension (partition ではない)](README.ja.md#phase-は第-3-の-dimension-partition-ではない)
参照。Governance 固有の帰結: **Quadrant 4 を operation phase に置くと
ADR-0010 が呼び出される**。Quadrant 4 の controls に Phase-crossing
decision (deployment 時に記録) が加わる。Design phase の Quadrant 4
配置は自動的に満たす。

Operation phase workflow の経験的 default は Quadrant 1 + 3 (+ 該当時
2)。Deviation は [`decision tree`](decision-tree.ja.md) Q0 + Q4 path
で表に出る。

## 象限ごとの詳細

### (1) Script Quadrant — 射程外

LLM 関連 ADR は適用されない。失敗分析は通常の SE channel を通じて
code author か pipeline owner にルーティングされる。

### (2) Algorithmic Search Quadrant — 射程外

本 repository の LLM 中心 ADR の射程外。失敗は algorithm author か
上流仕様 owner に行く。

### (3) LLM Workflow Quadrant — Medium governance

[README — LLM Workflow Quadrant](README.ja.md#3-llm-workflow-quadrant)
参照 (operational signal)。6 本の ADR が適用される:

| ADR | この象限で要求すること |
|---|---|
| [0001 Security by Absence](../adr/0001-security-by-absence.ja.md) | Harness は shell コマンド実行や arbitrary outbound HTTP のコードを ship しない。各 LLM 呼び出しは documented schema を持つ narrow な関数を介して呼び出される。 |
| [0003 Untrusted Content Boundary](../adr/0003-untrusted-content-boundary.ja.md) | RAG content、蓄積 state、外部から fetch した content は prompt に注入する際 untrusted として扱う。 |
| [0004 Single External Adapter](../adr/0004-single-external-adapter.ja.md) | Workflow を deploy 単位として外部 adapter は最大 1 つ。複数 surface の workflow は per-surface deployment に分割する。 |
| [0005 Human Approval Gate](../adr/0005-human-approval-gate.ja.md) | Workflow の prompt、role 定義、schema、routing logic への変更は human approval gate を通る。Runtime 振る舞いではなく workflow 自体が gate される。 |
| [0006 Causal Traceability](../adr/0006-causal-traceability.ja.md) | 各 LLM 呼び出しの input、output、role identifier、timestamp を log する。Workflow の制御フローは log から再構成可能。 |
| [0007 Scaffolding Visibility](../adr/0007-scaffolding-visibility.ja.md) | Prompt、role 定義、schema、routing rule、workflow の制御フローは version-controlled なファイル。 |

ADR-0008 (One Agent, One Human) はこの象限で strictly required では
ない — workflow が audit chain を保つなら team で運用可能 — が、
workflow が外部 side effect を持つ場合は accountable operator の
naming は同じ logic に従い recommended。

ADR-0002 (Deterministic Prohibition at Scaffolding Layer) と ADR-0009
(Triage Before Autonomy) は表内にないが、それは象限を **跨ぐ** 形で
適用されるから: 0002 は 0001 で absence が達成できない時の fallback、
0009 はそもそもこの象限を選んだ triage そのもの。

#### LLM Workflow Quadrant の redirect 経路

Workflow が誤った出力を出した時、operator は audit log を辿って失敗
した部品を特定する:

1. **どの LLM 呼び出しが誤った結果を返したか?** → その呼び出しの role
   owner (model 選定、prompt 設計、schema 仕様)。
2. **誤った呼び出しが routing error によって呼び出されたか?** →
   routing-logic designer。
3. **呼び出しの入力が誤っていたか?** → upstream-data owner (data
   ingestion、RAG index curator、prior-step role owner)。

各ステップに単一 owner がいる。Distribution が機能する。

### (4) Autonomous Agentic Loop Quadrant — High governance

[README — Autonomous Agentic Loop Quadrant](README.ja.md#4-autonomous-agentic-loop-quadrant)
参照 (operational signal と attribution-gap commitment)。10 本の
ADR すべてが適用される。ADRs 0005, 0006, 0008, 0009 が最も
load-bearing — これらが named gap-bearer の commitment を operational
にする。ADR-0010 は配置が operation phase の場合 Phase-crossing
decision を加える。

| ADR | この象限で要求すること |
|---|---|
| [0001 Security by Absence](../adr/0001-security-by-absence.ja.md) | Agent は持つべきでない capability を ship しない。Runtime tool 選択が任意 capability の呼び出しを意味するため、ここで特に load-bearing。 |
| [0002 Deterministic Prohibition at Scaffolding](../adr/0002-deterministic-prohibition-at-scaffolding.ja.md) | Absence にできない capability は LLM の **外** で PreToolUse hook や structural quarantine で gate する。 |
| [0003 Untrusted Content Boundary](../adr/0003-untrusted-content-boundary.ja.md) | Episode log、蓄積メモリ、self-authored distillation はすべて loop に読み戻す時 untrusted として扱う。 |
| [0004 Single External Adapter](../adr/0004-single-external-adapter.ja.md) | One agent process = one external adapter。Multi-surface deploy は agent の分割が要る。 |
| [0005 Human Approval Gate](../adr/0005-human-approval-gate.ja.md) | **Load-bearing**。あらゆる behavior-modifying write — skills、rules、identity、constitution — が named human を通り、承認・却下の双方に audit record を残す。この gate は runtime の全アクションに "OK" をクリックさせるものではない; runtime の side effect は ADR-0001/0002/0004 が構造的に bound する。 |
| [0006 Causal Traceability](../adr/0006-causal-traceability.ja.md) | **Load-bearing**。Loop の iteration、tool 呼び出し、observation、prompt は append-only で log される。部品レベル redirect が閉ざされていても、causal trace は post-incident reconstruction を可能にしなければならない。 |
| [0007 Scaffolding Visibility](../adr/0007-scaffolding-visibility.ja.md) | Loop の prompt template、available tools、system prompt、終了条件は version-controlled なファイル。 |
| [0008 One Agent, One Human](../adr/0008-one-agent-one-human.ja.md) | **Load-bearing**。Agent process に名前のある人間が 1 人結びつけられる。Rotation は formal なら compatible。 |
| [0009 Triage Before Autonomy](../adr/0009-triage-before-autonomy.ja.md) | **Load-bearing**。Triage decision と named gap-bearer は deployment 時に記録される。この象限への default 流入や post-incident な gap-bearer naming は禁止。 |
| [0010 Phase Separation](../adr/0010-phase-separation.ja.md) | **Operation phase 配置で load-bearing**。Phase-crossing decision（フェーズ越境判定）を deployment 時に記録: operation 中に新パターンが現れた時、autonomous loop が in place で動的に handle するか、design に feedback として戻すか? どちらの答えも admissible; 「起きたら考える」は不可。Design phase 配置 (コーディングエージェント、Deep Research) は自動的に満たす。 |

#### Autonomous Agentic Loop Quadrant の redirect 経路

この象限では部品レベル redirect は機能しない。*principled redirect
impossibility（原理的な振り替え不能）* は本質的に内在する: model
output、tool 選択、history reference、prompt context が runtime で
ブレンドし、事後分離可能性を閉ざす。

したがって redirect は別レベルで動く:

1. **System レベルの failure** が ADR-0006 causal traceability を
   通じて identify される — どの判断要素がどれだけ寄与したかは示せ
   なくても、loop が *failed* したことは trace で示せる。
2. **責任は pre-named gap-bearer に着地** (ADR-0008 + ADR-0009)。
   Gap-bearer は attribution gap を pre-deployment commitment として
   引き受けた operator。
3. **Gap-bearer が post-incident action を決定** — 再訓練、
   re-architect (LLM Workflow Quadrant に業務を移す)、deployment 退役。
   Post-incident analysis が単一責任部品を identify できないため、
   post-incident action は部品レベルではなく architectural レベルで
   動く。

これは (3) と比べて redirect の degraded 形 — distribution ではなく
1 名で終端する — だが、*moral crumple zone（道徳的クランプルゾーン）*
(operationally proximate だが負担の commitment はしていない) 失敗
ではなく **redirect** である。

## Cross-quadrant 注: prohibition-strength hierarchy

ADR 0001 → 0002 → 0003 が [prohibition-strength
hierarchy](../glossary.ja.md#prohibition-strength-hierarchy禁止強度の階層)
を成す。象限固有の帰結:

- LLM Workflow Quadrant では、scaffolding enforcement (0002) が
  workflow が誤って呼び出すべきでない capability を gate する。
- Autonomous Agentic Loop Quadrant では、scaffolding enforcement
  (0002) が essential — runtime tool 選択は probabilistic prohibition
  が prompt injection 下で hold しないことを意味する。
