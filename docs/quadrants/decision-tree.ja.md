言語: [English](decision-tree.md) | 日本語

# Decision Tree — 業務を Quadrant に振り分ける

> **目的。** ADR-0009 [Triage Before
> Autonomy](../adr/0009-triage-before-autonomy.ja.md) と ADR-0010
> [Phase Separation](../adr/0010-phase-separation.ja.md) を 5 質問の
> navigator に変換し、design review で実用できる形にしたもの。各分岐
> は対応する ADR (Quadrant 確定後に load-bearing になるもの) を参照
> する。固定 framework ではなく **judgment chain navigator** として
> 機能する。

## 5 つの質問

Q1–Q4 は [ADR-0009 の problem-space triage](../adr/0009-triage-before-autonomy.ja.md)。
Q0 は [ADR-0010](../adr/0010-phase-separation.ja.md) の Phase-crossing
decision（フェーズ越境判定）を、業務が operation phase かつ Q1–Q4 の
結果が Autonomous Agentic Loop Quadrant の時に表に出す。

Phase と Quadrant は独立な dimension
([README — Phase は第 3 の dimension (partition ではない)](README.ja.md#phase-は第-3-の-dimension-partition-ではない)
参照)。Q0 は Quadrant フィルタではなく、Phase-crossing decision が
load-bearing になる場面を表に出すための prompt。

### Q0. これは design phase の業務か、operation phase の業務か?

- **Design phase** (探索的、ワークフロー組み立て中、prototyping、
  未知構造の解析) → Q1–Q4 に進む。業務に応じて全 Quadrant が legitimate
  candidate。コーディングエージェント / Deep Research は design-phase
  resident として典型的にここに常駐 (各 session が独立した design
  session)。
- **Operation phase** (deploy された workflow が既知経路上を走り、
  予測可能性が要件) → Q1–Q4 に進む。業務に応じて全 Quadrant が
  candidate だが、*もし* Q1–Q4 が Autonomous Agentic Loop Quadrant を
  返したら、Phase-crossing decision を Q4 Part B で明示記録する。
  Operation phase workflow の経験的 default は Quadrant 1 + 3
  (+ 該当時 2) だが、これは default であって requirement ではない。

Phase の判断自体を deployment と並べて記録する。「これは operation
phase、default composition (Quadrant 1 + 3)」は完全な記録。「これは
operation phase、Quadrant 4 admitted、Phase-crossing decision: 新
パターンを design に戻す」も完全な記録。「決めなかった」は不完全。

## 4 つの problem-space 質問

### Q1. 業務は決定論ルールで書けるか?

業務をルールだけで完全に specify できるか — 「この入力 shape に対し
これらの手順でこの出力を返す、意味判断不要」。フォーム入力、データ
検証、閾値判定、スキーマ変換、lookup-and-format 系の業務。

- **Yes →** Q2 に進む。
- **No →** Q3 に進む (LLM がどこかで loop に入る)。

「決定論」のテスト: 同じ入力と同じ仕様を渡されたエンジニア 2 人が、
互いに相談せずに同一の出力を出せるか? Yes なら意味判断は不要。

### Q2. 探索空間は静的・有限か?

決定論的業務の中で、*列挙可能* なタスクと *探索的* なタスクを区別する。

- **Yes →** [`Script Quadrant`](README.ja.md#1-script-quadrant)。
  本 repository の 10 本の ADR は概ねこの業務の射程外。
- **No →** [`Algorithmic Search Quadrant`](README.ja.md#2-algorithmic-search-quadrant)。
  本 repository の LLM 中心の ADR の射程外。標準的な SE accountability
  が適用される。

### Q3. ワークフローを事前定義できるか?

LLM 関連業務にとっての中心的 triage 質問。「ワークフロー」とは
*各 LLM 呼び出しの役割、呼び出しの順序、呼び出し間のデータフロー*
を意味する。

- **Yes →** [`LLM Workflow Quadrant`](README.ja.md#3-llm-workflow-quadrant)。
  これが現行 LLM 応用の default。ADR-0001、0003、0004、0005、0006、
  0007 を適用する。各 ADR がこの象限で具体的に何を要求するかは
  [`governance-mapping.ja.md`](governance-mapping.ja.md#3-llm-workflow-quadrant--medium-governance)
  参照。
- **No →** Q4 に進む。

「ワークフローが事前定義可能」のテスト: 動かす **前** にホワイトボード
に LLM 呼び出しのシーケンスを描けるか? 各呼び出しの役割と schema を
ラベリングできるか? Yes なら workflow は事前定義可。次の呼び出しの
役割が前の呼び出しの出力次第で変わる場合、ワークフローは探索的。

### Q4. Pre-named gap-bearer（事前命名された責任引受者）を指名できるか? かつ (Q0 = operation の場合) Phase-crossing decision は記録されているか?

ここまで来た業務は本当に
[**Autonomous Agentic Loop Quadrant**](README.ja.md#4-autonomous-agentic-loop-quadrant)
を要求している。
[`attribution gap`](../glossary.ja.md#attribution-gap寄与の事後分離不能ギャップ)
は本質的に内在する; 分離可能な寄与のレベルでの redirect は機能しない。

質問は 2 part 構成; deployment には両方を yes と答える必要がある。

**Part A (常時必要)。** Attribution gap を引き受けるのは誰か?
ADR-0009 はこれを post-incident discovery に委ねることを禁止する
(*moral crumple zone（道徳的クランプルゾーン）* 失敗モード)。

**Part B (Q0 = operation phase の場合のみ)。** Phase-crossing
decision は記録されているか — operation 中に新パターンが現れた時、
autonomous loop が in place で動的に handle するか、design phase に
feedback として戻すか? ADR-0010 は operation-phase Quadrant 4 配置に
implicit な Phase-crossing decision を許さない。Design phase 配置では
この part は自動的に satisfied (design phase が Phase-crossing が
起きる場所そのもの)。

- **Yes (両 part) →** [`Autonomous Agentic Loop Quadrant`](README.ja.md#4-autonomous-agentic-loop-quadrant)。
  **10 本の ADR すべて** を適用 (各 ADR の per-quadrant breakdown は
  [`governance-mapping.ja.md`](governance-mapping.ja.md#4-autonomous-agentic-loop-quadrant--high-governance)
  参照; ADRs 0005, 0006, 0008, 0009, 0010 が最も load-bearing)。
  Gap-bearer (と該当時は Phase-crossing decision) を architectural
  choice と並べて deployment 時に記録。
- **No →** **Decision: deploy しない**。Quadrant 4 業務が gap-bearer
  なしで走る (Part A 失敗) か、operation で明示 Phase-crossing
  decision なしで走る (Part B 失敗) — ADR set が防ぐべき configuration。

「gap-bearer identifiable」のテスト: deployment record が、特定の
人間 (ADR-0008 通り)、formal succession を持つ organizationally
identifiable role、もしくは contractual party (insurance pool、
regulatory body) を指名できるか? 彼らは deployment 時にこの業務の
失敗の責任を引き受けると acknowledge できるか? どれも指名できなければ
答えは No。

「Phase-crossing decision recorded」のテスト: deployment record が、
operation 中に出現した新パターンに何が起きるかを 1 文で書けるか?
「design に feedback として戻す」「loop in place で handle する」の
どちらも admissible。「起きたら考える」は admissible でない。

## Hybrid アーキテクチャ

実システムはしばしば象限を混在させる。ADR-0009 は hybrid を許容する
が、それは象限の境界が構造的選択であり、Autonomous Agentic Loop 部分
が named gap-bearer 付きで扱われる場合のみ。

よくある形:

- **Plan-and-Execute** — autonomous loop が plan を生成、LLM Workflow
  が各ステップを実行する。Autonomous 部分は計画 phase。
- **Router agent** — LLM Workflow の中で routing 呼び出しが入力を
  分岐に分類する。Router 自体は bounded、分岐も bounded。
- **Tiered handoff** — LLM Workflow が common case を handle、
  novel case は (approval-gated) Autonomous Agentic Loop に escalate。

各 hybrid について、各部分に decision tree を別々に走らせる。
Autonomous 部分には Q4 の答えが必要。

## Triage を再実行するタイミング

Triage は design-time decision だが、業務の成熟に合わせて答えが変わる:

- **Phase 移行**。Design phase で始まる業務は経路が判明するに従って
  operation に成熟することが多い。これが起きたら Q0 を再実行する;
  結果として Quadrant placement が 4 から 3 に移動するかもしれない
  (regression ではなく正当な成熟)。
- **Quadrant 移行**。Autonomous Agentic Loop Quadrant で始まる業務は
  経路が判明したら LLM Workflow Quadrant に移ることが多い。Q1–Q4 を
  再実行。
- **Vendor capability の変化**。以前は届かなかった capability
  (Q1=No, Q3=No) が model / tooling の進化で workflow-able になる
  ことがある。Quadrant 4 から 3 へ。
- **Phase-crossing decision の変更**。「新パターンを design に戻す」
  と記録した operation 配置が、経験を経て「loop in place で handle」
  に切り替わることがある (逆も)。Phase-crossing decision を
  architectural change と並べて再記録。
- **規制の変化**。Q4 の答え (該当ドメインで gap-bearer が指名できるか)
  を変えうる。

迷ったら 5 質問を再実行し、新しい答えを architectural change と並べて
文書化する。
