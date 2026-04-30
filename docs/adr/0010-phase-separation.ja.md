言語: [English](0010-phase-separation.md) | 日本語

# ADR-0010: Phase Separation Between Design and Operation (experimental)（設計フェーズと運用フェーズの分離 — experimental）

> **Summary。** *Phase（フェーズ）* (design vs operation) と
> *Quadrant（象限）* (problem-space) は deployment の独立した dimension。
> 両 phase が任意の Quadrant の component を host できるため、この ADR は
> phase-to-quadrant の mapping ではない。記録される判断はもっと narrow:
> **Autonomous Agentic Loop Quadrant の component を operation phase の
> default として置くことは、runtime 駆動の attribution gap を production
> 環境に流し込む** — そこでは ADR-0009 の design-time commitment 単独で
> はもはや十分でない。そのような placement は依然 admissible だが、
> ADR-0009 の gap-bearer naming に加えて、documented な Phase-crossing
> decision（フェーズ越境判定）を要求する。

## Status
**experimental** — design / operation 区別は agent context の外でよく
確立されている (development vs production、as-is vs operation、
design-time vs runtime) が、AAP Quadrants への翻訳は最近 (2026-05-01)
で *operation-phase default* に対する運用上計測可能な signal はまだ
詰めている途中。末尾の open question 参照。

## Date
2026-05-01 (本 repository での初 formalize)

## Context

ADR-0009 ([Triage Before Autonomy](0009-triage-before-autonomy.ja.md))
は「この業務は autonomous-loop アーキテクチャを採用すべきか?」に
[Business AI Quadrants](../glossary.ja.md#business-ai-quadrants業務-ai-四象限)
で routing することで答えた。2026-05-01 essay は関連するが distinct な
問いを表に出す: *答えが yes なら、autonomous loop は lifecycle のどこに
住むのか、そしてその placement は design-time triage が逃した cost を
持つのか?*

Essay の argument は categorical な mapping ではなく観察と提案として
提示されている。全体の言い回し —「実は…ではないか?」「…として release
できないか?」— は診断的であって prescriptive ではない。AAP はその姿勢
を引き継ぐ: 下記の rule は narrow で、周辺の context は deploying team の
*思考刺激*。

### Phase and Quadrant are independent（Phase と Quadrant は独立）

Phase / Quadrant landscape は二次元 — 各 Quadrant は実 system では
両 phase に出現する。Framing と orthogonality 議論は
[`quadrants/README.ja.md` — Phase は第 3 の dimension](../quadrants/README.ja.md#phase-は第-3-の-dimension-partition-ではない)
を参照。

4×2 cross-product は典型的な配置を named する:

| | Design phase | Operation phase |
|---|---|---|
| Script Quadrant | prototype スクリプト | production pipeline |
| Algorithmic Search Quadrant | algorithm prototyping | production solver |
| LLM Workflow Quadrant | design review での prototype workflow | production-default の LLM workflow |
| Autonomous Agentic Loop Quadrant | コーディングエージェント、Deep Research、探索的 R&D | 連続的に進化する research operation、探索的 analytics |

4×2 matrix は landscape であって 1-to-1 mapping ではない。AAP が記録
する narrow な判断は「Quadrant N は X-phase の道具」ではない。それは:
*Autonomous Agentic Loop Quadrant の operation-phase 配置は、
ADR-0009 の design-time commitment 単独ではカバーできない構造的 cost
を持つ*。

### Why operation-phase autonomous loops are the load-bearing case（なぜ operation-phase autonomous loop が load-bearing case か）

ADR-0009 の gap-bearer naming は attribution gap を *deployment
commitment として* 扱う。これが機能するのは、ADR-0009 が設計時に
framed されたから: architectural choice の瞬間に、deployer が gap を
acknowledge し、それを carry する subject を name する。

Operation-phase placement はその上に second-order risk を加える: 単に
「gap が存在し誰かが bear する」だけでなく、「**gap が production
runtime 内で予期しないパターンに対して発火する、しかも gap-bearer は
loop に入っていない**」。2026-05-01 essay の中心観察: 新パターンが
operation で表に出る時、autonomous-loop アーキテクチャはそれを動的に
absorb する — そしてその動的 absorption は、redirect を閉ざす同じ
ブレンドで runtime を動かし続ける。ADR-0009 の人為的 redirect 不能性
が再び現れる、今度は design-time mismatch としてではなく deploy された
system 内で。

これが deploying team が考えるべきことであって、blanket な
phase-to-quadrant mapping ではない。

### The three-layer diagnosis（3 層診断 — エッセイ 4-6）

| Layer | 診断 | Essay |
|---|---|---|
| 表層 | Quadrant 誤適用 — (3) 業務が (4) アーキテクチャ経由で routing される | Essay 4 (2026-04-29) |
| 中層 | 語彙 gap — LLM Workflow Quadrant の肯定形の名前がない | Essay 5 (2026-04-30) |
| 深層 | Phase 混同 — 「always-on autonomous agent が業務を回す」が design + operation を 1 system に圧縮する | Essay 6 (2026-05-01) |

誤適用は語彙 gap から育ち、語彙 gap は phase 混同から育つ。ADR-0010 は
最深層を *診断フレーム* として記録する、partition rule としてではなく。

## Decision (experimental)

Phase / Quadrant landscape は deployment の design space。その中で、
AAP は以下の narrow な判断を記録する:

1. **Operation-phase の Autonomous Agentic Loop placement は、ADR-0009
   の gap-bearer naming に加えて、明示的な Phase-crossing decision を
   要求する。** Phase-crossing decision は 1 文で答える: *operation で
   新パターンが表に出た時、autonomous loop が in place で動的に handle
   するか、design phase に feedback として戻すか?* 両方の答えとも
   admissible。「起きた時に考える」は admissible でない。
2. **Operation-phase の default 構成は Quadrant 1 + 3 (+ 該当時 2)。**
   これは default であって禁止ではない。経験的観察を反映する: 経路が
   設計時に knowable な workflow の大半は、redirect が部品レベルで
   成功する構成によってよりよく serve される。Default からの deviation
   は (1) が満たされていれば admissible。
3. **任意の Quadrant の cross-phase 使用は normal。** 同じ Quadrant が
   同じプロジェクトの design phase と operation phase に出現しうる —
   prototype された (design) 後に promote された (operation) workflow、
   発見が workflow に組み込まれる前 (operation) に explore する
   (design) ために使われた autonomous loop。判断は placement ごとで、
   Quadrant ごとではない。
4. **Feedback path が新 operation-phase パターンへの recommended
   response。** 新パターンが operation で表に出て (1) が「design に
   routing」と決定されていた場合、handoff は明示的: パターンに tag を
   打ち、design phase に escalate し、design phase が新パターンが
   Quadrants のどこに fit するかを決めるまで operation surface を
   default 構成に戻す。これは recommendation であって hard requirement
   ではない; 代替 (in-place dynamic handling) は (1) のもとで admissible
   で、明示的に選ばれる。

これらの条件が named されない時、(2) の default が適用される: operation
deployment は Quadrant 1 + 3 (+ 2) の component で動き、明示的な
Phase-crossing decision なしに operation に流れ込んだ Autonomous
Agentic Loop placement は ADR-0010 への non-compliance になる。

### What this is *not* saying（これが *言っていない* こと）

- **「Autonomous Agentic Loop は design phase 専用」と言っていない。**
  Operation-phase の placement は admissible。ただし (1) の追加 commitment
  を carry する。
- **「LLM Workflow Quadrant は operation phase 専用」と言っていない。**
  Bounded LLM 呼び出しを使った design-phase prototyping は通常で正当。
- **Quadrant を Phase で partition していない。** Phase / Quadrant
  landscape は二次元; この ADR はその 1 セル (operation-phase Quadrant
  4) について 1 つの load-bearing 観察と、もう 1 つのセル (operation-phase
  composition) について default を記録する。
- **何も ban していない。** Team の Phase-crossing decision を *暗黙
  ではなく明示にする* だけ。Rule は procedural であって architectural
  ではない。

### What this permits（これが許すこと）

- **同じ workflow が両 phase に出現する。** Routing prompt を design で
  prototype し operation に promote する workflow は通常の lifecycle で、
  Phase boundary 違反ではない。
- **Phase-crossing decision を記録した operation-phase Autonomous Agentic
  Loop。** これは admissible。Deployment は named した cost に対して
  accountable。
- **Deploy された service のように見える design-phase resident。**
  コーディングエージェント、Deep Research tool、探索的ブラウジング
  agent — これらは連続的に走るが、各 session は独立した design session
  で探索が終われば complete する。Continuous availability は deployment
  レベルの事実で、各 session の phase レベルの性質ではない。

### What this does not permit（これが許さないこと）

- **暗黙的な Phase-crossing decision。** Deployment 時に documented な
  Phase-crossing decision なしの operation-phase Autonomous Agentic Loop
  placement は non-compliance。
- **ADR-0009 gap-bearer なしの operation-phase autonomous loop。**
  ADR-0009 が既にこれを要求する; ADR-0010 はそれを relax しない。

## Alternatives Considered

- **Strict ban: autonomous loop は operation phase に決して入らない。**
  Reject。Records と観察は正当な edge case (高頻度の探索的 analytics、
  連続的に進化する research operation) — design / operation 境界が
  blur する場所 — を示す。Categorical ban はそれら case を framework
  から書き落とすだけで、彼らが払う cost を named しない。
- **Phase-to-Quadrant 1-to-1 mapping (Autonomous Loop = Design phase、
  LLM Workflow = Operation phase)。** この ADR の前 draft が reject
  したのと同様に reject。実 system は Quadrant N を両 phase に日常的
  に置く; 1-to-1 mapping はそれらを framework から押し出す。Mapping は
  landscape であって partition ではない。
- **Strong guardrail で十分。** Phase-crossing decision の代用として
  reject。Guardrail (sandbox 強度、HITL 負荷) は blast radius を bound
  し review を加える; team の in-place absorption と feedback-to-design
  の選択を deployment 時の明示的決定として表に出さない。
- **Phase Separation を ADR-0009 に subsume する。** Reject。ADR-0009 は
  そもそも業務が Autonomous Agentic Loop Quadrant に属するかを triage
  する。ADR-0010 は ADR-0009 の答えが yes な placement について
  Phase-crossing decision を表に出す。両者は独立な triage 軸 (problem-space
  と time-axis); merge すると明確さと 3 層分析の診断フレーム価値の両方
  を失う。

## Consequences

- Repository の構造は **10 ADRs + 4 Business AI Quadrants** になり、
  ADR-0009 と ADR-0010 が *triage pair* を成す: 0009 は「どの Quadrant
  か?」を尋ね、0010 は答えが Autonomous Agentic Loop Quadrant の時に
  Phase-crossing decision を表に出す。
- コーディングエージェント、Deep Research、類似する always-on の
  探索的 system は明示的に *service として deploy された design-phase
  resident* と named される。AAP のもとでの彼らの正当性は、彼らを
  category exception に押し込まずに保たれる。
- Operation-phase deployment は透明な構成 (Quadrant 1 + 3 + 該当時 2)
  に default され、redirect が部品レベルで成功する。Deviation は
  admissible で明示的。
- LLM Workflow Quadrant や Autonomous Agentic Loop Quadrant の
  design-phase 使用は normal で flag されない。
- Post-incident reconstruction が「agent は何をしたか?」だけでなく
  「これが起きた時、deployment はどの Phase-crossing decision のもとで
  動いていたか?」にも答えられる。答えは deployment record にあり、
  推測ではない。
- 3 層診断 (エッセイ 4-6) が design review のための恒久的診断フレーム
  になる: autonomous loop が operation に提案されたら層を歩く。多くの
  resolution は phase 質問が出る前に誤適用 (層 1) や語彙 gap (層 2) を
  表に出す。

## Open questions (why this is experimental)

1. **Operation-phase Autonomous Agentic Loop の edge case。** 高頻度の
   探索的 analytics、連続的に進化する research operation、bounded
   されないパターンに対する real-time content moderation — これらは
   Phase-crossing decision の「in place で handle」が構造的に正直な
   答えになりうる domain。AAP は「ここでは Phase-crossing in place が
   正しい」を「この team は誤適用を rationalize している」から区別する
   経験的 signal をまだ持っていない。
2. **Sequence-of-design-sessions framing。** コーディングエージェント
   や Deep Research tool が独立した design session の sequence である
   ことをやめて operation-phase deployment になるのはいつか? 構造的
   基準 (各 session が complete; state が operation-relevant な形で
   session 間に蓄積されない) は vendor-built system では解釈の余地が
   ある。
3. **Recursion。** Design phase 自体が autonomous (agent が自身の次の
   experiment を設計する) な時、rule は recurse するか? 「design
   session」と「design system」の境界は未解決。
4. **Operation-phase placement での ADR-0009 との interaction。**
   Phase-crossing decision は gap-bearer と並ぶ *2 つ目の* commitment を
   構成するか (1 つは attribution gap、1 つは phase-crossing risk)、
   それとも同じ subject が両方か? 実務では同じ人間 / role が両方であ
   ることが多い; structural な分離が必要かは open。
5. **Vendor framing。** 現行の autonomous-loop product の大半は
   operation-phase deployment として marketing される。AAP-aligned な
   deployment は、vendor 資料が default で「operation = autonomous-loop
   runtime」と仮定していても Phase-crossing decision を明示しなければ
   ならない。これが AAP が提供すべき counter-vocabulary を要求するか
   は open。

この ADR は実 deployment での Phase-crossing decision の運用経験が
蓄積するに従って改訂される。Status は edge case が documented answer
を持つようになれば experimental から accepted に動き、design /
operation 区別が正しい cut でないとテストで判明すれば rejected /
superseded に動く。

---

**Lineage。**
[6 部作 narrative spine](../inspiration.md) の記事 6 (2026-05-01)
が design / operation 区別と 3 層診断 (誤適用 → 語彙 gap → phase
混同) を articulate した。Argument は categorical な phase-to-quadrant
mapping としてではなく、観察と提案として提示されている; ADR-0010 は
その姿勢を保つ。ADR-0010 は
[ADR-0009](0009-triage-before-autonomy.ja.md) と parallel: 0009 は
業務が autonomous loop を採用するかを triage し、0010 は 0009 の答え
が yes で placement が operation-phase の時に Phase-crossing decision
を表に出す。Design / operation 区別は agent context の外に存在する
(development vs production、as-is vs operation、design-time vs
runtime); agent context は cut を発明したのではなく、import した。
