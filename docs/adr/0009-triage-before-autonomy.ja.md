言語: [English](0009-triage-before-autonomy.md) | 日本語

# ADR-0009: Triage Before Autonomy (experimental)（自律採用の前に triage する — experimental）

> **Summary。** Autonomous-loop アーキテクチャ (agent が runtime で
> 各次のステップを決めるもの) を採用する前に、業務を 2 軸で triage
> する — 決定論 vs 意味判断、事前定義 vs 探索的。Autonomous loop は
> open-ended な探索を本当に要求する象限のために予約する。Autonomous
> loop を選ぶことは、deploy する組織に除去できない attribution gap を
> named な pre-deployment コストとして引き受けることを commit する —
> post-incident な発見ではなく。

## Status
**experimental** — この原理は trilogy (2026-04-06 / 04-13 / 04-14) と
architectural follow-up (2026-04-29 / 04-30) から構造的に導かれるが、
post-hoc separable なアーキテクチャと blend されたものを design 時に
区別するための運用 signal はまだ詰めている途中。末尾の open question
参照。

## Date
2026-04-30 (本 repository での初 formalize)

## Context

Trilogy と先行する 8 本の ADR は「何を制約すべきか、誰が責任を負うか」
に答える。各答えは滅多に明示されない構造的前提を仮定している:
system 内の各判断要素は **post-hoc separable** — 何かが起きた時、
operator は失敗を特定の部品 (model 選定、routing rule、上流データ source)
に trace し、責任をそこに routing できる。これが
[`accountability distribution`](../glossary.ja.md#accountability-distribution責任の分配)
を機能させる。Distribution は redirect される subject を要求する。

ReAct 型の autonomous loop はこの前提に違反する。Autonomous-loop アーキ
テクチャでは、各 iteration の役割が runtime で決まる — model output、
tool 選択、history 参照、prompt context が単一の判断 stream にブレンド
する。Output が誤った時、各寄与を事後にほどけない。
[`Attribution gap`](../glossary.ja.md#attribution-gap寄与の事後分離不能ギャップ)
がこの閉ざされた separability の名前。これは autonomy に本質的に内在
する性質で、より良い tooling が解決する成熟度問題ではない。

2026-04-29 essay は業務 AI の 4 象限分解を 2 軸で導入した:

| | Pre-defined workflow | Exploratory |
|---|---|---|
| **Deterministic** | Script Quadrant | Algorithmic Search Quadrant |
| **Semantic-judgment** | LLM Workflow Quadrant | Autonomous Agentic Loop Quadrant |

2026-04-30 essay は、業界の標準語彙に LLM Workflow Quadrant の肯定形
の名前がないことを観察した。「決定論的でないもの」が消去法で
Autonomous Agentic Loop Quadrant に routing される。結果として、互いに
混同されてきた 2 つの異なる redirect failure mode が現れる:

- **Artificial redirect impossibility（人為的な振り替え不能）。** LLM
  Workflow Quadrant 業務が autonomous-loop アーキテクチャに routing
  されている。Re-architect で resolvable。
- **Principled redirect impossibility（原理的な振り替え不能）。**
  Autonomous Agentic Loop Quadrant 業務、ブレンドが本当に redirect を
  閉ざす。アーキテクチャ的には resolve しない。

現状の accountability 議論の多く (sandbox 強度、HITL 過負荷、
*moral crumple zone* — Elish 2019) は人為的 case を扱う。原理的 case —
業務が本当に open-ended な探索を要求 *かつ* attribution は依然分配
されねばならない時に何をするか — はほぼ言説に入っていない。

先行する ADR 群はこれを単独で resolve できない。なぜならこの gap を
作る architectural choice が、それら ADR が記述するすべての制約の上流
にあるから。Sandbox は blast radius を bound する (ADR-0004); redirect
可能性は復元しない。Approval gate は外部 side effect を gate する
(ADR-0005); ブレンドされた判断を separate しない。Triage decision は
他 ADR が applicable になる前の設計時に属する。

## Decision (experimental)

Autonomous-loop アーキテクチャを採用する前に、業務を 4 象限の 2 軸で
triage する:

1. **Script Quadrant** (決定論 × 事前定義)。スクリプトと pipeline を
   使う。LLM 不要。
2. **Algorithmic Search Quadrant** (決定論 × 探索的)。古典アルゴリズム
   を使う — 探索、動的計画法、MCTS、強化学習。本 repository の LLM
   中心 ADR の射程外。
3. **LLM Workflow Quadrant** (意味判断 × 事前定義)。Structured-workflow
   アーキテクチャを使う: 決定論的制御フロー + named で documented
   role を持ち explicit な input/output schema を持つ bounded LLM
   呼び出し。これが現行 LLM 応用の大半に対する documented default。
4. **Autonomous Agentic Loop Quadrant** (意味判断 × 探索的)。*業務
   自体が open-ended な探索を要求する時のみ* autonomous loop を使う —
   経路が事前予測不能で、LLM 呼び出しの役割を bounded にすると業務が
   そもそも遂行できなくなる。

Autonomous Agentic Loop Quadrant を選ぶことは、deploy する組織に
attribution gap を **named な pre-deployment コスト** として引き受け
ることを commit する。Named cost は 2 つの component を持つ:

- **Gap を carry する organizationally identifiable な subject。** 現状
  の実務では、これは人間 (ADR-0008 [One Agent, One
  Human](0008-one-agent-one-human.ja.md) と整合)。Agent 自身であっては
  ならない; agent はまだ legal subject ではない。Post-incident に解決
  してはならない; *moral crumple zone* 文献は、その負担が実際の
  control が最も少ない operator に落ちることを示す。
- **Deployment record における明示的 acknowledgement** で、この業務の
  redirect は分離可能寄与のレベルでは成功しないこと。Failure 分析は
  system が *failed した* ことを identify する; clean な redirect
  target を produce しない。

Triage decision は設計時に人間によって行われ、architectural choice と
並べて記録される。

### What this permits（これが許すこと）

- **象限を組み合わせる Hybrid アーキテクチャ** は、両者の境界自体が
  構造的選択であり、Autonomous Agentic Loop 部分が named gap-bearer
  付きでそのように扱われるなら compatible。例: 条項抽出を LLM
  Workflow Quadrant で行い、novel-clause 分析を approval-gated な
  Autonomous Agentic Loop に escalate する。
- **業務が成熟するにつれて象限を移すこと。** 探索的に始まる業務は
  経路が判明したら structured になることが多い; Autonomous Agentic
  Loop から LLM Workflow への re-architect は正当な成熟であって
  regression ではない。
- **LLM Workflow Quadrant 内で LLM が行う routing 決定** (Anthropic の
  *routing* pattern)。Routing は documented schema を持つ単一の
  bounded judgment; runtime で判断をブレンドしない。

### What this forbids（これが禁じること）

- **意味的だが事前定義可能な業務に Autonomous Agentic Loop を default
  すること。** 本 repository が named する category error。
- **Triage を runtime に deferral すること。** Autonomy に入る決定
  自体が architectural commitment; いつ escalate するかを agent に
  決めさせると、triage が motivate した同じ gap を継承する。
- **Post-incident に gap-bearer を named すること。** Deployment 時に
  organizationally identifiable な subject が named されていなければ、
  業務は *gap-bearer なし* で Autonomous Agentic Loop Quadrant にある —
  この ADR が防ぐために存在する configuration。
- **Sandbox 強度を triage の代用とすること。** Sandboxing は blast
  radius を bound する (ADR-0004 領域)。Separable contribution は
  復元しない。

## Alternatives Considered

- **Default で autonomous-loop アーキテクチャを採用; guardrail で
  制約する。** Reject。Guardrail は ADR-0001 critique の意味での
  signpost。Architectural choice 自体が redirect を閉ざした; どんな
  guardrail もそれを復元しない。
- **Autonomy を strong に sandbox して安全にする。** 代替として
  reject。Sandboxing は blast radius (ADR-0004) を扱うのであって、
  redirect 可能性ではない。完璧に sandbox された autonomous loop でも
  attribution gap を持つ。
- **Triage を runtime に deferral; loop からの escalation を agent に
  決めさせる。** Reject。Runtime triage は autonomous な決定で、同じ
  gap を継承する。決定は設計時に属する。
- **4 象限を業務カテゴリではなく agent カテゴリとして扱う。** Reject。
  象限は *業務* を述べる、agent ではない。同じ agent process が異なる
  象限の業務に対して deploy されうる; 変わるのはその業務に対して選ぶ
  アーキテクチャ。
- **Triage を skip し、ADR-0005 (approval gate) と ADR-0008 (one
  operator) に結果を catch させる。** Reject。両 ADR は autonomy 下で
  load-bearing が *増す*、減るのではなく。Architectural choice が既に
  破壊した separability を復元することはできない。

## Consequences

- LLM Workflow Quadrant が現行 LLM 応用の大半に対する documented
  default になる。語彙はその default を「real」 agent からの逸脱と
  して扱うのではなく normalize する。
- Autonomous Agentic Loop が *選ばれる* 時、コストは前払いされる。
  ADR-0005 (approval gate)、ADR-0006 (causal traceability)、ADR-0008
  (one operator) は autonomy 下で load-bearing が増す、減るのではなく。
- Designer は post-hoc separable なアーキテクチャと blend されたもの
  を設計時に区別しなければならない。実用的 signal: 各 LLM 呼び出しが
  documented role を持つ; 各呼び出しが input/output schema を持つ;
  失敗が特定の部品に map する。これら signal が hold しないなら、
  そのアーキテクチャは Autonomous Agentic Loop Quadrant にある —
  そう named されていたかどうかに関係なく。
- 4 象限分解が 10 本の ADR と pair される恒久的な診断フレームになる
  — 共に二軸構造を成す: ADR が問いに答え、Quadrants が業務をその
  答えがあてはまる場所に振り分ける。
- Attribution-gap commitment を named せずに autonomous-loop template
  を publish する vendor や reference architecture は、ADR-0001 が言う
  *climbable な壁の signpost* をやっている — 本 repository が反対する
  ために設計された pattern。

## Open questions (why this is experimental)

1. **Boundary case。** Bounded な system-prompt 定義 role を持つ
   multi-turn chat agent — LLM Workflow Quadrant に数えるほど separable
   か、それとも既に Autonomous Agentic Loop 領域か? 答えは role bound
   が prompt injection と異常な user input 下で hold するかに依存する;
   基準はまだ sharp ではない。
2. **Post-hoc separability の運用上計測可能な proxy。**「各呼び出しの
   寄与が separable」は現状 design judgment。経験的 signal (run 間の
   call-graph 安定性、撹乱下での role 一貫性、schema 遵守率) が judgment
   をより transferable にしうるが、正しい metric set は open。
3. **責任 subject が存在しない時の ADR-0008 との interaction。** 業務
   が本当に Autonomous Agentic Loop Quadrant を要求 *かつ* どの個人の
   権限も超える gap を引き受けられる organizationally identifiable な
   subject がいない (大規模な法的・医療的・財政的 action) 時、正しい
   決定は deploy しないことか、contractual party (insurance pool、
   regulatory body) を bearer として deploy することか、まだ存在しない
   institutional infrastructure を待つことか? これは manifesto 領域。
4. **規制 regime との interaction。** 一部の domain は Autonomous
   Agentic Loop Quadrant を直接規制するだろう (EU AI Act の high-risk
   分類が近い前例)。Triage decision がそうした regime を name で参照
   すべきか、architectural に framed のままにして regulation を Quadrants
   に外側から map させるべきかは未解決。

この ADR は実 deployment での Quadrants 語彙の運用経験が蓄積するに
従って改訂される。Status は boundary case が documented answer を持つ
ようになれば experimental から accepted に動き、triage frame が誤った
frame だとテストで判明すれば rejected / superseded に動く。

## Consequences for the other ADRs（他 ADR への帰結）

ADR-0009 は適用順序で ADRs 0001–0008 の手前に位置する:

- **Script Quadrant** — ADRs 0001–0008 は概ね射程外; 業務は LLM 装備
  agent を伴わない。
- **Algorithmic Search Quadrant** — 本 repository の射程外。
- **LLM Workflow Quadrant** — ADRs 0001–0007 が適用される。Workflow が
  side effect を送る external adapter を持つなら ADR-0008 が適用される;
  operator binding は意味があるが lighter weight。
- **Autonomous Agentic Loop Quadrant** — *9 本すべて* の ADR が適用
  され、ADR-0005、ADR-0006、ADR-0008 が最も weight を持つ — これらが
  named gap-bearer の commitment を operational にするから。

これが ADR-0009 が ADRs 0001–0008 と同じ document set に属する理由:
後継としてではなく、与えられた業務に対して 8 本のうちのどれが load-bearing
になるかを決める triage として。

---

**Lineage。** 記事 4 ("[Where ReAct Agents Are Actually Needed in
Business](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant.md)",
2026-04-29) が業務 AI の 4 象限 framing を導入した。記事 5 ("[The LLM
Workflow Quadrant Is Missing from Our
Vocabulary](https://github.com/shimo4228/zenn-content/blob/main/articles-en/react-agent-business-quadrant-2.md)",
2026-04-30) が principled vs artificial redirect impossibility、moral
crumple zone (Elish 2019)、不在の accountability subject を named した。
先行 essay の source 命名 ("ReAct Quadrant"、"Classical AI Quadrant") は
[`../glossary.ja.md`](../glossary.ja.md) で alias として保存される;
この ADR は Decision body を harness-neutral に保つため改名形を使う。
ADR-0008 ([One Agent, One Human](0008-one-agent-one-human.ja.md)) と
parallel: 0008 が accountable human を bind し、0009 が 0008 の binding
を意味あるものにする architectural choice を bind する。
