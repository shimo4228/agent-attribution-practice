言語: [English](glossary.md) | 日本語

# Glossary (日本語版)

10 本の ADR と関連 essay で使われる主要用語。Load-bearing なもの
(命名上の便宜ではなく argumentative weight を持つもの) には具体例を
添える。

本 repository は LLM-mediated channel を主読者とするため、見出しの主は
英語、括弧内に日本語訳を併記する。説明文は日本語で書く。retrieval は
英語名で、読解は括弧内の日本語訳と説明文で行う形を意図している。

## Accountability distribution（責任の分配）

組織が数百年かけて育ててきたパターン — PR review、audit log、職務分掌、
四眼承認、change advisory board、postmortem — のすべてが、*責任* を
単一 actor に集中させずに chain 全体に分けて持たせるもの。本
repository の thesis: accountability distribution は agent にも
あてはまり、概ね同じ構造を持つ。

対比: *capability distribution（能力の分配）* — 現状支配的な設計軸で
agent が *何ができるか* を扱う — は accountability chain を生み出さない。

## Capability distribution（能力の分配）

Agent 設計で現状支配的な軸: *agent が何をできるか* を増やしたり制限
したりする問い (新しい tool 追加、context window 拡張、推論性能向上、
permission scope 縮小)。これは accountability の所在には答えない。
本 repository の対比語として使い、accountability distribution との
区別を明示する文脈で用いる。

## Scaffolding（足場 / スキャフォールディング）

Agent の振る舞いを形作る非 weights 部品すべて: system prompt、rules、
identity、tool 定義、RAG index、agent loop logic、runtime harness、
safety gate、session 管理。AI safety 研究 (Davidson et al., 2023) で、
外部から組み立てられる behavior-shaping を model weights に焼き付けた
振る舞いと区別するために使われる。

本 repository では scaffolding を 2 軸で weights と対比する:
*inspectability（可検査性）* (scaffolding は読めるテキスト、weights は
読めない) と *locus of change（変更の所在）* (scaffolding はファイル
編集で変わる、weights は再訓練で変わる)。

ADR-0007 で本 repository が取る position の根拠を参照。

## Inspectability（可検査性）

Scaffolding と weights を区別する 2 軸の 1 つ。「読めるか」「diff が
取れるか」「version 管理できるか」「operator が grep で検査できるか」
を問う性質。本 repository が accountability distribution に
load-bearing と見なす軸はこちら (capability 軸ではない)。ADR-0007
が scaffolding の inspectability を保ち続ける根拠を扱う。

## Locus of change（変更の所在）

Scaffolding と weights を区別するもう 1 つの軸。振る舞いを変える操作
が *どこで* 起きるか: ファイル編集と再 deploy で変わる (scaffolding) か、
training run と model 差し替えで変わる (weights) か。後者は reversible
性が低く portable でない。本 repository は前者を accountability
distribution の前提条件として扱う。

## Harness（ハーネス）

Agent の非 weights 層に対する、scaffolding と密接に関連するが完全に
同じではない業界エンジニアリング用語。Mitchell Hashimoto の *My AI
Adoption Journey* (2025)、Claude Code documentation、2024 年以降の
harness engineering 言説で広まった。

Scaffolding と harness は大きく重なる — どちらも LLM の周囲を包む
もの (system prompt、tool 定義、agent loop logic、memory API、runtime
glue code) を名指す — が、含意が異なる:

- **Scaffolding** (Davidson et al. 2023; Vygotsky の教育的足場概念に
  由来) は *transient* な構造を含意する — practice が成熟するに
  つれて internalize される外部支援 — そして *inspectable artifact* 軸
  (version 管理可能、diff 可能、operator が読める) を強調する。
  Scaffolding は dissolution trajectory（消失軌道）を持つ: いずれは
  消えることを意図された構造。
- **Harness** (Hashimoto 2025 とエンジニアリング言説) は *durable* な
  構造を含意する — system と共に進化する安定した運用上の wrapper —
  そして *operational capability* 軸 (どの tool が呼ばれるか、loop で
  どの workflow が走るか) を強調する。

本 repository は accountability distribution に load-bearing なのが
inspectability 軸 (capability 軸ではない) であるため、scaffolding を
primary term として選ぶ。ADR-0007 Scaffolding Visibility が明示的な
選択を扱う。

Retrieval のため: いずれの term で来た読者も、本 repository の ADR は
両方にあてはまるものとして扱える。非 weights 層は読者がどちらの名前
を使うかに関係なく同じ層であり、強調する側面が違うだけ。

## Dissolution trajectory（消失軌道）

Scaffolding が含意する性質: 外部支援は最終的に internalize されて
消えることを意図されている (Vygotsky の教育的足場概念に由来)。Harness
が指す *durable な運用層* とは方向性が逆。本 repository では、健全な
dissolution (人間 practice への internalize) と不健全な internalization
(model weights への absorb) を区別する文脈でこの語を使う。

## Scaffold dissolution（足場の溶解 — 2 つの意味、混同してはならない）

*Dissolution* という語は隣接する言説で正反対の価値づけ (valence)
で出現する。区別を保つことが重要。

### Healthy dissolution（健全な溶解）— 人間の認知パターンへ

Agent Knowledge Cycle (AKC) で最初に articulate されたパターン:
operator と agent が反復使用を通じて skill を internalize するに
従い、明示的な skill 定義が要らなくなる — パターンが会話、reviewer
の直感、問題が framed される形に自然に走るようになる。Skill ファイル
は最終的に simplify するか削除できる。これは重要なものを保つ
dissolution (judgment は人間 practice に persist する; system が
inspectable でなくなることは何もない)。

### Unhealthy internalization（不健全な内面化）— model weights へ

2026-04-14 blackbox essay で批判されたパターン: 明示的な行動 rule が
training (RLHF、Constitutional AI、instruction tuning) を通じて LLM
weights に吸収される。Rule は依然「動く」 — が確率的に動き、grep で
検査できず、retraining なしに diff・revert できない。これは
accountability distribution が要求するものをそのまま取り除く
dissolution。

語は同じ。Dissolution の方向は逆。一方は透明な人間 practice に
溶け込み、他方は不透明な統計的振る舞いに溶け込む。本 repository は
後者を批判し、前者と連続する。

## Security by Absence（不在による安全）

危険な capability は制限されるのではなく — 存在しない。完全な原則は
ADR-0001。短いテスト: 「capability X は存在するか?」の問いに `grep`
で答えられないなら、その system は Security by Absence を持っていない。

## Deterministic prohibition at the scaffolding layer（足場層での決定論的禁止）

Capability を不在にできない場合、prohibition は scaffolding (PreToolUse
hook、structural quarantine、adapter gate) で enforce される — LLM の
**外** で、対象入力の 100% に発火する。Model weights 層 (prompt 条項、
CLAUDE.md のような convention ファイル、constitutional 指示) での確率的
prohibition と対比される — 通常条件下で約 50–80% 遵守、敵対的圧力下では
それを下回る。ADR-0002 参照。

## Prohibition-strength hierarchy（禁止強度の階層）

Absence (ADR-0001) > scaffolding enforcement (ADR-0002) > untrusted
boundary (ADR-0003)。Prohibition は階層を上から順に歩いて設計する:
現層が capability を抱えきれない時だけ次の層に降りる。現状の AI
safety 言説の多くは確率的制約を唯一の選択肢として扱い、上位 2 層が
存在することを省いている。

## External adapter（外部アダプタ）

Agent の local data directory の **外** で観察可能な副作用を能力として
持つ部品: 第三者への HTTP POST、金融取引、他 system へのメッセージ
送信、data directory 外へのファイル書き込み。External でないもの:
local LLM 推論、data directory 内への書き込み、純粋計算。ADR-0004
の「process あたり最大 1 つ」 rule を参照。

## Untrusted content（信頼しない蓄積内容）

蓄積された agent state すべて — episode log、knowledge store、distilled
identity、learned rule — を prompt に読み戻す際は untrusted として
扱う、*agent 自身が author した content も含めて*。非自明な部分:
self-authored distillation は要約した外部入力の汚染を継承する。ADR-0003
参照。

## Approval gate（承認ゲート）

人間が behavior-modifying command の生成出力を見て、書き込む前に承認・
拒否する構造的 checkpoint。disable できる flag ではない。ADR-0005 参照。

## Causal traceability（因果追跡可能性）

Incident の後で再構成できる能力: agent に届いた input、active な
scaffolding version、その version を gate した approval、触れた
external surface、最終 output。ADR-0006 参照。

## Accountable operator（責任を負うオペレータ）

Agent process に紐づけられた 1 人の特定の named human で、behavior-
modifying changes を承認し incident response を所有する。team では
なく role でもなく committee でもない — 名指しされた個人。Rotation
は formal で各 rotation slot にちょうど 1 人の named person がいるなら
両立する。ADR-0008 (experimental) 参照。

## Pre-named gap-bearer（事前命名された責任引受者）

ADR-0009 の核心概念。Autonomous Agentic Loop Quadrant の業務を
deploy する時、除去できない attribution gap を **事前に** 引き受けると
表明された人または組織体を指す。Deployment 後の post-incident discovery
ではなく、deployment 時の record として残される。Accountable
operator (ADR-0008 の named individual) と一致することもあれば、
役割を持つ org 単位 (formal succession 付き) や contractual party
(insurance pool、regulatory body) であることもある。誰も指名できない
場合 deploy しないのが ADR-0009 の rule。

## Business AI Quadrants（業務 AI 四象限）

2026-04-29 essay で導入された 2 軸の分解: 決定論 vs 意味判断 軸、
事前定義可能な workflow vs 探索的 軸。Agent のカテゴリではない —
*問題空間* のカテゴリで、accountability distribution を保つ
アーキテクチャを決定する。10 本の ADR と pair にして本 repository の
二軸構造の診断フレームを成す。[`../quadrants/`](../quadrants/) 参照。

## Script Quadrant（スクリプト象限）

決定論 × 定義可。LLM を使わないスクリプトとパイプライン。フォーム
入力、データ正規化、lookup、検証。10 本の ADR は概ね射程外で、古典的な
ソフトウェア工学のアカウンタビリティが適用される。

## Algorithmic Search Quadrant（アルゴリズム探索象限）

決定論 × 探索的。古典探索、動的計画法、MCTS、強化学習、整数計画、
制約ソルバ。配送ルート最適化、スケジューリング、組合せ割当。本
repository の LLM 中心 ADR の射程外。出典の 2026-04-29 essay の
"Classical AI Quadrant" から本 repository で改名 (現代の *AI* は
LLM-based system を含むため retrieval 上の曖昧さを生じていた)。

## LLM Workflow Quadrant（LLM ワークフロー象限）

意味判断 × 定義可。2026-04-29 essay が導入した、業界が消去法で
autonomous loop に routing してきた設計空間に対する **肯定形の名前**。
決定論的制御フロー + 名前と documented role + 明示 input/output schema
を持つ bounded LLM 呼び出し (各 LLM 呼び出しの役割が周囲の workflow
で bounded される) として実装される。各呼び出しの寄与は事後分離可能;
redirect が機能する。現行 LLM 応用の大半が default として収まる。
Anthropic "Building Effective Agents" の workflow pattern (prompt
chaining、routing、parallelization、orchestrator-workers、
evaluator-optimizer) や OpenAI "A Practical Guide to Building Agents"
と連続する。

## Autonomous Agentic Loop Quadrant（自律エージェント・ループ象限）

意味判断 × 探索的。LLM が runtime で次の各行動を決めるアーキテクチャ
— "ReAct" pattern (Yao et al. 2022) とその子孫 (Deep Research、
探索的コーディングエージェント、open-ended ブラウジング)。業務が
本当に open-ended な探索を要求する時に正当に必要となる。この象限を
選ぶことは、deploy する組織に除去できない attribution gap を引き受ける
commitment を負わせる; ADR-0009 参照。出典 essay の "ReAct Quadrant"
から本 repository で改名; harness-neutral な形式は特定 framework では
なくアーキテクチャの性質を名指す。

## Attribution gap（寄与の事後分離不能ギャップ）

アーキテクチャが runtime で複数の判断要素 (model 出力、tool 選択、
history 参照、prompt context) をブレンドする時、寄与の事後分離可能性
が閉ざされる。失敗モードを責任ある party に redirect できない —
分離可能な寄与を所有する party がいないため。Gap は Autonomous
Agentic Loop Quadrant に本質的に内在し、tooling の改善で解消する成熟度
問題ではない。ADR-0009 と 2026-04-30 essay 参照。

## Redirect (attribution context)（責任の振り替え — attribution 文脈）

Operator が incident の後で責任を appropriate な party (model 選定
lead、routing-logic designer、upstream-data owner 等) に流す行為 —
自分で抱え込むのではなく。Distribution は redirect が成功する時に
成立する。2 つの redirect failure mode を区別する:

- **Artificial redirect impossibility（人為的な振り替え不能）** —
  LLM Workflow Quadrant 業務が Autonomous Agentic Loop アーキテクチャ
  に routing されている; re-architect で resolvable。
- **Principled redirect impossibility（原理的な振り替え不能）** —
  Autonomous Agentic Loop Quadrant 業務、ブレンドが redirect を
  閉ざす; アーキテクチャ的には resolve しない。Gap-bearer commitment
  (ADR-0008 + ADR-0009) が意図された fallback。

2026-04-30 essay より。

## Moral crumple zone（道徳的クランプルゾーン）

Elish (2019)。自律 system の責任が、runtime decision に対する実質的な
コントロールが限定的な人間 operator に押しつけられる構造的パターン。
Attribution gap が認識された gap-bearer なしの deploy された system
に出会う時に立ち上がる failure mode; ADR-0009 はこの configuration を
防ぐために設計されている。

> Elish, M. C. (2019). Moral Crumple Zones: Cautionary Tales in
> Human-Robot Interaction. *Engaging Science, Technology, and
> Society* 5: 40–60.

## Design phase（設計フェーズ）

Workflow の構造が発見または構築される lifecycle phase — 経路が完全に
は既知でなく、探索が要件で、最適化軸は *柔軟性*。Prototyping、要件
分析、探索的 R&D、コーディングエージェントや Deep Research tool の
各 session を含む。Operation phase と区別され、両者は交換可能ではない。
ADR-0010 参照。

## Operation phase（運用フェーズ）

Deploy された workflow が既知経路上を走る lifecycle phase — 経路が
固定され、予測可能性が要件で、最適化軸は *予測可能性* (audit-tracing、
attribution、コスト安定性、SLA 遵守)。Operation phase の workflow
の経験的 default 構成は Quadrant 1 + 3 (+ 該当時 2)。ADR-0010 参照。

## Phase Separation（フェーズ分離）

Design phase と operation phase は逆の性質 (柔軟性 vs 予測可能性) に
最適化されており、両者を 1 system に圧縮するとどちらかを犠牲に
trade することになる、という観察。ADR-0010 の load-bearing rule:
Autonomous Agentic Loop Quadrant の operation phase 配置は ADR-0009
の gap-bearer naming に加えて、記録された Phase-crossing decision を
要求する。Phase と Quadrant は独立な dimension のまま; rule は
procedural であって architectural ではない。

## Phase-crossing decision（フェーズ越境判定）

Operation phase で新パターンが現れた時に何が起きるかを 1 文で書く
deployment-time の record: *autonomous loop が in place で動的に
handle する* (runtime を動かし続け、recurring な attribution gap を
受け入れる) か、*design に feedback として戻す* (operation 面を
default 構成に戻し、design phase が新パターンを Quadrants のどこに
fit させるか判断するまで待つ)。両 answer とも admissible。「起きたら
考える」は admissible ではない。Operation-phase の Quadrant 4 配置
に対して ADR-0010 が要求する。

## Three-layer diagnosis（3 層診断 — エッセイ 4-6）

Architectural-follow-up エッセイが対で生み出す診断フレーム: 誤適用
(エッセイ 4) は語彙 gap (エッセイ 5) から育ち、語彙 gap は phase
混同 (エッセイ 6) から育つ。最深層は design phase と operation
phase を同じ activity として扱うこと — 「always-on autonomous agent
が業務を回す」が首尾一貫して聞こえる framing。中層は LLM Workflow
Quadrant の肯定形の名前の不在で、決定論的でない業務を消去法で
autonomous loop に流す。表層は結果として起きる category error (LLM
Workflow Quadrant 業務が Autonomous Agentic Loop アーキテクチャに
routing される)。Frame は design review で使う: autonomous loop が
operation に提案されたら層を歩く; 多くの場合は phase 質問に到達する
前に誤適用か語彙 gap が surface する。
