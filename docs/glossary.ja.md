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

## Externalized accountability（外部化された責任）

システムが便益を私的に内部化し、害を外へ押し出すとき、*それが誰の
判断だったか* を言う能力も害とともに外部化される。その能力は消えず、
二つの経路のいずれかをとる。帰結が **nameable（名指せる）** — 特定
actor の行為に帰属でき、制度が受け止められる — とき、怒りは訴訟・
補償・規制へ流れる。**un-nameable（名指せない）** — 害が拡散し、原因が
辿れない — とき、怒りは最も見える単一の個人へ暴力的に収束する
（scapegoat mechanism）。ADR に記録された内部判断（causal traceability、
minimum disclosure set、事前に名指された gap-bearer）こそが、帰結を
nameable 側へ移す。この読みでは、accountability distribution は
ガバナンスであるだけでなく暴力防止の仕組みである。

これは *social-consequence layer* の中心用語であり、ADR から分離して
置かれる規範的拡張である。[`social-consequence.ja.md`](social-consequence.ja.md)
参照。同じ判断を監査・事故後再構成として扱う ADR 内部の枠組みと対比される。

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

## Sponsor (vendor terminology)（sponsor — 業界語彙）

Microsoft Entra Agent ID (2026-05-01) が導入した *sponsor* 概念:
各 agent に責任者を割り当て、孤立した認証情報の蓄積を防ぐ。Sponsor
制度は administrative ownership の mechanism。AAP の
[*pre-named gap-bearer*](#pre-named-gap-bearer事前命名された責任引受者)
(ADR-0009 / ADR-0008 参照) とは区別する: gap-bearer は **deployment
時に named された structural な accountability commitment** で、
attribution gap 失敗を引き受ける subject。同一人物が両方を兼ねうるが、
product 上で sponsor が named されていても gap-bearer commitment
(legal subject、authority、contractual standing) を acknowledge
していないケースは、AAP の意味では gap-bearer になっていない。
Sponsor は gap-bearer の必要条件だが十分条件ではない。
[Industry mapping](industry-mapping.md)
参照。

## Agent governance (vendor terminology)（agent governance — 業界語彙）

業界 vendor (Microsoft Agent 365、AWS Bedrock、Google Vertex AI、
identity-security スタートアップ) が *agent governance* というラベルで
ship する mechanism 群: identity 管理、policy enforcement、
observability、cross-cloud registry sync、audit log 相関。AAP の
*accountability distribution* とは区別する: governance は「何が起きて
いるかを観測し、policy を enforce する mechanism」、accountability
distribution は「失敗時に誰にどう redirect するかの構造」。Governance
は accountability distribution の必要条件だが十分条件ではない —
logging と policy enforcement は Autonomous Agentic Loop Quadrant
(ADR-0009) に内在する attribution gap を閉じない。
[Industry mapping](industry-mapping.md)
参照。

## Observability vs structural enforcement（observability と構造的 enforcement の区別）

2026 Q2 の industry release が表に出した階層的区別。
*Observability* (cross-cloud agent registry sync、audit log 相関、
runtime monitoring) は **既に複数の external surface を獲得した agent**
に対して動作し、事後に何が起きたかを再構成する。
*Structural enforcement* (ADR-0001 Security by Absence、ADR-0002
deterministic prohibition、ADR-0004 Single External Adapter) は
**設計時に surface 数を bound** し、blast radius を観測可能にする
だけでなく構造的に制限する上流側の判断。Observability は事後に blast
radius を狭めることはできず、structural enforcement はすでに漏れた
ものを教えてくれない。両者は階層が違う mechanism stratum に属する。
entro.security の *cross-environment governance gap* 観察は、
「既に壊れた設計の上での observability は次善」という証拠として読める。
[Industry mapping](industry-mapping.md)
参照。

## Business AI Quadrants（業務 AI 四象限）

2026-04-29 essay で導入された 2 軸の分解: 決定論 vs 意味判断 軸、
事前定義可能な workflow vs 探索的 軸。Agent のカテゴリではない —
*問題空間* のカテゴリで、accountability distribution を保つ
アーキテクチャを決定する。10 本の ADR と pair にして本 repository の
二軸構造の診断フレームを成す。[`quadrants/`](quadrants/) 参照。

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
**Load-bearing な性質:** *実行パスは事前に決まっており (人間、コード、
周囲のワークフローによって)、LLM はそのパス内の単一の bounded な
ステップとして呼び出される*。LLM は次のアクションを決めない — 次の
アクションは calling pipeline またはセッションを回している人間
オペレータが既に決定している。現行 LLM 応用の大半が default として
収まる。

本象限は入出力モダリティで自然に 2 つの sub-form に分かれる:

- **(3a) Conversational sub-form（対話 sub-form）** — 検索、システムプロンプト、
  (必要に応じ) 会話履歴を bounded な LLM 呼び出しと組み合わせる
  specialized chat agents。例: 法律相談支援、診断支援、内部 FAQ、
  専門知識サポート。会話中の人間が *判断主体 (judging agent)* であり、
  LLM は知識検索・整理を担う。Multi-turn な対話も、各ターンの LLM
  呼び出しが文書化された役割を持ち、人間 (LLM ではなく) が次に何を
  するかを決めている限り、この sub-form に収まる。
- **(3b) Batch sub-form（バッチ sub-form）** — 制御フローが通常のコード
  (本 repo の実装では Python が中心だが、アーキテクチャは言語非依存) で
  書かれ、semantic-judgment の leaf 点が *LLM 関数* で扱われる、普通の
  コードベース。(3b) のアーキテクチャ的プリミティブは **LLM 関数
  (LLM function)**: コードベース内の通常の関数で、関数本体は LLM 呼び出しに
  委譲し、入力型・出力スキーマ・単一の判断責務を持つ (例:
  `match_line_items(invoice_lines, po_lines) -> {MATCH, PARTIAL, NO_MATCH}`)。
  コードベースが制御フローを所有し、LLM 関数はコードベースが決定論的に
  判断できない leaf 点のみを占める。例: invoice matching、ticket triage、
  RPA 上の例外分類、address normalization、feed 関連度スコアリング +
  コメント生成。重要なのは、(3b) は汎用エージェントを *必要としない*
  ── 50 判断カテゴリなら 50 個の narrow な関数であって、1 つの汎用関数では
  ない。

Post-hoc separability ── オペレータがどの呼び出しがどの寄与を生んだ
かを特定し責任を redirect できる ── は load-bearing な性質の
*帰結* であり essence ではない。個々の呼び出しの確率的揺らぎは性質を
破らない ── 各呼び出しで固定されているのは呼び出しあたりの出力で
はなく、呼び出しが果たす *役割* (周囲コードによって固定される) だから
である。Anthropic (2024) と OpenAI (2025) は composition pattern
(prompt chaining、routing、parallelization、orchestrator-workers、
evaluator-optimizer; manager pattern、decentralized pattern) を文書化
しており、これらはより広い Q3 領域で動くが、セル自体を肯定形では命名
していない ── 文書はパターンを *workflows* と銘打って *agents* と対比し、
workflow カテゴリは残余として残されている。Vocabulary gap の診断は
essay 5 を参照。

> Lineage: essay 4 (2026-04-29) が象限と conversational / batch
> sub-form の区別を導入。essay 5 (2026-04-30) が肯定形の名前の不在を
> artificial redirect impossibility の構造的原因として診断した。

## LLM 関数 (LLM function)

LLM Workflow Quadrant の (3b) Batch sub-form のアーキテクチャ的
プリミティブ。**コードベース内の通常の関数で、関数本体は LLM 呼び出しに
委譲し、入力型・出力スキーマ・単一の判断責務を持つもの**。呼び出し側
から見れば、LLM 関数は他の関数と同じように振る舞う ── 定義された入力を
渡せば、定義された型の値が返る ── ただし出力は確率的に揺らぎうる
(LLM が判断器官だから)。

代表例:

- `match_line_items(invoice_lines, po_lines) -> Verdict` (Essay 4
  2026-04-29): 2 つの line item set が意味的に対応するかを判定し、
  `MATCH` / `PARTIAL` / `NO_MATCH` を返す。
- `score_relevance(post_text) -> float` (Contemplative Agent feed
  manager): 投稿の関連度を `[0.0, 1.0]` で返す。
- `generate_comment(post_text) -> Optional[str]` (Contemplative Agent
  コメントパイプライン): agent の constitution に基づくコメント文字列を
  生成する。

各々は周囲のコードベース内の通常の関数である。関数自身は次に何をするかを
決めない ── 呼び出し側 (周囲コードの決定論的制御フロー) が決める。
汎用エージェントとの対比: LLM 関数は設計上 narrow である。50 判断カテゴリ
なら 50 個の narrow な関数になり、1 つの汎用関数にはならない。

Anthropic (2024) と OpenAI (2025) は同じ Q3 領域で動く composition
pattern (prompt chaining、routing、parallelization、orchestrator-
workers、evaluator-optimizer; manager pattern、decentralized pattern)
を文書化している。これらは orchestration pattern であり、LLM 関数を
プリミティブとして用いうる (routing classifier は LLM 関数そのもの)
が、pattern 自体は LLM 関数ではない。

> Lineage: essay 4 (2026-04-29) が `match_line_items` 例と「50 カテゴリなら
> 50 関数」framing で LLM 関数概念を導入。Contemplative Agent が
> feed 処理パイプラインで同パターンを operationalize。

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
procedural であって architectural ではない。Essay 7 (2026-05-02) が
*capability-as-secondary（能力は従因）* 観察を加えた: より高い LLM
capability でも、業務を Quadrant 3 ↔ Quadrant 4 gradient 上のどこに
着地させるかを決める target identifiability や scale-resilience の差
は埋められない。Phase が主因で、capability は下流。

## Phase-crossing decision（フェーズ越境判定）

Operation phase で新パターンが現れた時に何が起きるかを 1 文で書く
deployment-time の record: *autonomous loop が in place で動的に
handle する* (runtime を動かし続け、recurring な attribution gap を
受け入れる) か、*design に feedback として戻す* (operation 面を
default 構成に戻し、design phase が新パターンを Quadrants のどこに
fit させるか判断するまで待つ)。両 answer とも admissible。「起きたら
考える」は admissible ではない。Operation-phase の Quadrant 4 配置
に対して ADR-0010 が要求する。

## Skill-design gradient（skill 設計勾配）

Essay 7 (2026-05-02) で導入された観察: skill 設計粒度では LLM
Workflow Quadrant (3) と Autonomous Agentic Loop Quadrant (4) の
境界が clean な dichotomy ではなく *連続 gradient*。Skill、または
skill 内の subcomponent は、その phase (design vs operation) と二次的
な力 (target identifiability と scale-resilience) によって gradient
上の位置を取る。同じジョブが、どちらの phase 向けに実装されているか
で gradient 上の異なる位置に着地する。ADR-0010 の Skill-design descent
subsection 内で informational frame として使われる。

## Phase descent（Phase 軸の下降）

Phase Separation 軸が business-system 粒度 (ADR-0010 の load-bearing
rule (1) が支配する Quadrant の deployment placement) から skill 設計
粒度、さらに **単一 skill 内の subcomponent 粒度** にまで降りる現象。
単一の skill が frozen-pipeline subcomponent (operation 形態) と
runtime-judgment subcomponent (design 形態) を矛盾なく混在させうる;
診断フレームは再帰的に適用される。Essay 7 (2026-05-02) で表に出た。
ADR-0010 のもとで informational であり、新しい load-bearing rule では
ない。

## Target identifiability（識別可能性）

[skill-design gradient](#skill-design-gradientskill-設計勾配)
上の二次的な力の 1 つ: skill (または subcomponent) が対象とする
artifact が固定 path / 命名規約に乗っているかどうか。高い target
identifiability は operation-phase の frozen-pipeline 実装の前提条件:
対象が固定された瞬間、以前は runtime に下す必要があった判断を
embedding、clustering、閾値ロジック等の決定論的機構として焼き込める。
対象が呼び出しごとに可変な場合は runtime LLM 判断が構造的に正直な
選択になる。Essay 7 (2026-05-02) より。

## Scale-resilience（スケール耐性）

[skill-design gradient](#skill-design-gradientskill-設計勾配)
上のもう 1 つの二次的な力: skill が呼び出しサイクルあたり処理する
単位数 (1 件 / N 件 / 全体 corpus) と、その規模での miss rate 許容度。
LLM 判断は単位数の増加とともに miss が累積するため、規模が大きいほど
実装は phase に関わらず決定論的形態へ押される。Essay 7 (2026-05-02)
より。

## Three-layer diagnosis（3 層診断 — エッセイ 4-7）

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
前に誤適用か語彙 gap が surface する。Essay 7 (2026-05-02) は同じ
Phase 軸を skill 設計粒度に descend させ、深層を拡張した: 診断フレーム
は単一 skill や skill 内 subcomponent にも再帰的に適用でき、gradient
形状は [target identifiability](#target-identifiability識別可能性) と
[scale-resilience](#scale-resilience-スケール耐性) によって支配される。
