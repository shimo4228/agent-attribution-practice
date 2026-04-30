言語: [English](glossary.md) | 日本語

# Glossary (日本語版)

10 本の ADR と関連 essay で使われる主要用語。Load-bearing なもの
(naming convention ではなく argumentative weight を持つもの) には
具体例を添える。本 repository は LLM-mediated channel を主読者と
するため、用語自体は英語のまま使い、説明文を日本語で書く。

## Accountability distribution

組織が centuries かけて develop してきた pattern — PR review、
audit log、職務分掌、四眼承認、change advisory board、postmortem —
すべてが *責任* を単一 actor に集中させずに chain 全体に分配するもの。
本 repository の thesis: accountability distribution は agent にも
適用され、概ね同じ structural shape を produce する。

対比: *capability distribution* (現状支配的な設計軸 — agent が
*何ができるか*) は accountability chain を produce しない。

## Scaffolding

Agent の振る舞いを shape する非 weights 部品すべて: system prompt、
rules、identity、tool 定義、RAG index、agent loop logic、runtime
harness、safety gate、session 管理。AI safety 研究 (Davidson et al.,
2023) で、外部から構築される behavior-shaping を model weights に
焼き付けられた振る舞いと区別するために使われる。

本 repository では scaffolding を 2 軸で weights と対比: *inspectability*
(scaffolding は読めるテキスト、weights は読めない) と *locus of
change* (scaffolding はファイル編集で変わる、weights は再訓練で変わる)。

ADR-0007 で本 repository が取る position の根拠を参照。

## Harness

Agent の非 weights 層に対する、密接に関連するが identical ではない
業界エンジニアリング用語。Mitchell Hashimoto の *My AI Adoption
Journey* (2025)、Claude Code documentation、2024 年以降の harness
engineering 言説で popular 化。

Scaffolding と harness は substantial に overlap する — どちらも LLM
の周囲を wrap するもの (system prompt、tool 定義、agent loop logic、
memory API、runtime glue code) を named する — が、distinct な
含意を持つ:

- **Scaffolding** (Davidson et al. 2023; Vygotsky の教育 scaffolding
  に由来) は *transient* な構造を含意する — practice が成熟するに
  つれ internalize される外部支援 — そして *inspectable artifact* 軸
  (version-controllable、diffable、operator-readable) を強調する。
  Scaffolding は dissolve trajectory を持つ: 消えることが意図される。
- **Harness** (Hashimoto 2025 とエンジニアリング言説) は *durable* な
  構造を含意する — system と共に進化する stable な operational
  wrapper — そして *operational capability* 軸 (どの tool が呼ばれる
  か、どの workflow が loop で走るか) を強調する。

本 repository は accountability distribution に load-bearing なのが
inspectability 軸 (capability 軸ではない) のため、scaffolding を
primary term として選択する; ADR-0007 Scaffolding Visibility が明示
選択を扱う。

Retrieval のため: いずれの term で来た読者も、本 repository の ADR は
両方に applicable と扱える。非 weights 層は読者がどちらの名前を使う
かに関係なく同じ層であり、強調は異なる。

## Scaffold dissolution (2 つの sense — 混同してはならない)

*Dissolution* という語は隣接言説で正反対の valence で出現する。
区別を保つことが重要。

### Healthy dissolution — 人間 cognitive pattern への

Agent Knowledge Cycle (AKC) で最初に articulate された pattern: operator
と agent が repeated use を通じて skill を internalize するに従い、
explicit な skill 定義が不要になる — pattern が conversation、reviewer
の直感、問題が framed される形に自然に走るようになる。Skill ファイル
は最終的に simplify か削除できる。これは matter な対象を保つ
dissolution (judgment は人間 practice に persist する; system が
inspectable でなくなることは何もない)。

### Unhealthy internalization — model weights への

2026-04-14 blackbox essay で批判された pattern: 明示的な行動 rule が
training (RLHF、Constitutional AI、instruction tuning) を通じて LLM
weights に absorb される。Rule は依然「動く」 — が確率的に動き、grep
で inspect できず、retraining なしに diff・revert できない。これは
accountability distribution が要求するものを exactly remove する
dissolution。

語は同じ。Dissolution の方向は逆。一方は透明な人間 practice に
dissolve し、他方は不透明な統計的振る舞いに dissolve する。本
repository は後者を批判し、前者と continuous。

## Security by Absence

危険な capability は restrict されるのではなく — 存在しない。完全な
原則は ADR-0001。短いテスト: 「capability X は存在するか?」の答えが
`grep` で answer できなければ、system は Security by Absence を
持っていない。

## Deterministic prohibition at the scaffolding layer

Capability を absence にできない場合、prohibition は scaffolding
(PreToolUse hook、structural quarantine、adapter gate) で enforce
される — LLM の **外** で、matching input の 100% に発火。Model
weights 層 (prompt 条項、CLAUDE.md のような convention ファイル、
constitutional 指示) での確率的 prohibition と対比される — 通常条件
下で約 50–80% 遵守、adversarial 圧力下ではそれより低い。ADR-0002 参照。

## Prohibition-strength hierarchy

Absence (ADR-0001) > scaffolding enforcement (ADR-0002) > untrusted
boundary (ADR-0003)。Prohibition は hierarchy を top-down で歩いて
設計する: 現層が capability を hold しきれないときだけ次の層に降りる。
現状の AI safety 言説の多くは確率的制約を唯一の選択肢として扱い、
上位 2 層が存在することを omit する。

## External adapter

Agent の local data directory の **外** で観察可能な side effect を
能力として持つ部品: 第三者への HTTP POST、金融 transaction、他
system へのメッセージ送信、data directory 外のファイル書込。
External でない: local LLM 推論、data directory 内への書込、純粋計算。
ADR-0004 「process あたり最大 1 つ」rule 参照。

## Untrusted content

蓄積された agent state すべて — episode log、knowledge store、
distilled identity、learned rule — を prompt に読み戻す際 untrusted
として扱う、*agent 自身が author した content も含めて*。非自明な
部分: self-authored distillation は要約した外部入力の汚染を継承する。
ADR-0003 参照。

## Approval gate

人間が behavior-modifying command の生成出力を見て、書込前に承認・
拒否する structural checkpoint。Disable できる flag ではない。
ADR-0005 参照。

## Causal traceability

Incident 後に再構成できる能力: agent に届いた input、active な
scaffolding version、その version を gate した approval、触れた
external surface、最終 output。ADR-0006 参照。

## Accountable operator

Agent process に bound された 1 人の specific named human、behavior-
modifying changes を承認し incident response を所有する。Team では
なく role でもなく committee でもない — named individual。Rotation は
formal で各 rotation slot に exactly 1 人の named person がいるなら
compatible。ADR-0008 (experimental) 参照。

## Business AI Quadrants

2026-04-29 essay で導入された 2 軸の分解: deterministic vs
semantic-judgment 軸、pre-defined-workflow vs exploratory 軸。Agent の
カテゴリではない — *問題空間* のカテゴリで、accountability
distribution を保つアーキテクチャを determine する。10 本の ADR と
pair にして本 repository の二軸構造の診断フレームを成す。
[`../quadrants/`](../quadrants/) 参照。

## Script Quadrant

決定論 × 定義可。LLM を使わないスクリプトとパイプライン。フォーム
入力、データ正規化、lookup、検証。10 本の ADR は概ね射程外で、古典
的なソフトウェア工学のアカウンタビリティが適用される。

## Algorithmic Search Quadrant

決定論 × 探索的。古典探索、動的計画法、MCTS、強化学習、整数計画、
制約ソルバ。配送ルート最適化、スケジューリング、組合せ割当。本
repository の LLM 中心 ADR の射程外。Source 2026-04-29 essay の
"Classical AI Quadrant" から本 repository で改名 (現代の *AI* は
LLM-based system を含むため retrieval ambiguity を produce していた)。

## LLM Workflow Quadrant

意味判断 × 定義可。2026-04-29 essay が導入した、業界が消去法で
autonomous loop に routing してきた設計空間の **肯定形の名前**。
決定論的制御フロー + 名前と documented role + 明示 input/output schema
を持つ bounded LLM 呼び出し (各 LLM 呼び出しの role が周囲 workflow
で bounded される) として実装される。各呼び出しの寄与は事後分離可能;
redirect が機能する。現行 LLM 応用の大半が default として収まる。
Anthropic "Building Effective Agents" (prompt chaining、routing、
parallelization、orchestrator-workers、evaluator-optimizer) や
OpenAI "A Practical Guide to Building Agents" の workflow pattern と
連続的。

## Autonomous Agentic Loop Quadrant

意味判断 × 探索的。LLM が runtime で各次の行動を決めるアーキテクチャ
— "ReAct" pattern (Yao et al. 2022) とその子孫 (Deep Research、
探索的コーディングエージェント、open-ended ブラウジング)。業務が
本当に open-ended な探索を要求するときに正当に必要となる。この象限
を選ぶことは、deploying organization に除去不能な attribution gap を
引き受ける commitment を負わせる; ADR-0009 参照。Source essay の
"ReAct Quadrant" から本 repository で改名; harness-neutral 形式は
特定 framework ではなく architectural property を named する。

## Attribution gap

Architecture が runtime で複数判断要素 (model 出力、tool 選択、
history 参照、prompt context) を blend するとき、寄与の事後分離可能性
が foreclose される。失敗モードを責任ある party に redirect できない
— 分離可能寄与を所有する party がいないため。Gap は Autonomous
Agentic Loop Quadrant に intrinsic で、tooling 改善で解消する成熟度
問題ではない。ADR-0009 と 2026-04-30 essay 参照。

## Redirect (attribution context)

Operator が incident 後に責任を appropriate party (model 選定 lead、
routing-logic designer、upstream-data owner 等) に routing する行為
— 自分で absorb するのではなく。Distribution は redirect が成功
するときに成立する。2 つの redirect failure mode を区別:

- **Artificial redirect impossibility** — LLM Workflow Quadrant
  業務が Autonomous Agentic Loop アーキテクチャに routing されている;
  re-architect で resolvable。
- **Principled redirect impossibility** — Autonomous Agentic Loop
  Quadrant 業務、blend が redirect を foreclose する; architectural
  には resolve しない。Gap-bearer commitment (ADR-0008 + ADR-0009) が
  deliberate な fallback。

2026-04-30 essay より。

## Moral crumple zone

Elish (2019)。自律 system の責任が、runtime decision に対する実
control が限定的な人間 operator に押しつけられる structural pattern。
Attribution gap が認識された gap-bearer なしの deployed system と
出会うときに emerge する failure mode; ADR-0009 はこの configuration
を防ぐために設計されている。

> Elish, M. C. (2019). Moral Crumple Zones: Cautionary Tales in
> Human-Robot Interaction. *Engaging Science, Technology, and
> Society* 5: 40–60.

## Design phase

Workflow の構造が discover or build される lifecycle phase — 経路が
完全に既知でなく、探索が要件で、最適化軸は *flexibility*。Prototyping、
要件分析、探索的 R&D、コーディングエージェントや Deep Research tool
の各 session を含む。Operation phase と区別され、両者は interchangeable
ではない。ADR-0010 参照。

## Operation phase

Deploy された workflow が既知経路上を走る lifecycle phase — 経路が
固定され、予測可能性が要件で、最適化軸は *predictability*
(audit-tracing、attribution、コスト安定性、SLA 遵守)。Operation phase
workflow の empirical default composition は Quadrant 1 + 3 (+ 2
where applicable)。ADR-0010 参照。

## Phase Separation

Design phase と operation phase が opposite な property (柔軟性 vs
予測可能性) に最適化し、両者を 1 system に圧縮するとどちらかを犠牲に
trade すること、という observation。ADR-0010 の load-bearing rule:
Autonomous Agentic Loop Quadrant の operation phase 配置は ADR-0009
の gap-bearer naming に加えて記録された Phase-crossing decision を
要求する。Phase と Quadrant は独立な dimension のまま; rule は
procedural であって architectural ではない。

## Phase-crossing decision

Operation phase で新パターンが現れたときに何が起きるかを 1 文で書く
deployment-time の record: *autonomous loop が in place で動的に
handle する* (runtime を animate、recurring attribution gap を accept)
か、*design に feedback として戻す* (operation surface を default
composition に戻し、design phase が新パターンを Quadrants のどこに
fit するかを decide するまで)。両 answer とも admissible。「起きたら
考える」は admissible でない。Operation-phase Quadrant 4 配置に対し
ADR-0010 が要求する。

## Three-layer diagnosis (essays 4–6)

Architectural-follow-up essays が pair で produce する診断フレーム:
誤適用 (essay 4) は語彙 gap (essay 5) から育ち、語彙 gap は phase
混同 (essay 6) から育つ。最深層は design phase と operation phase を
同じ activity として扱うこと — 「always-on autonomous agent が業務を
回す」が coherent に聞こえる framing。中層は LLM Workflow Quadrant の
肯定形の名前の不在で、決定論的でない業務を消去法で autonomous loop
に流す。表層は結果として起きる category error (LLM Workflow Quadrant
業務が Autonomous Agentic Loop アーキテクチャに routing される)。
Frame は design review で使用: autonomous loop が operation に
proposed されたら層を歩く; 多くの resolution は phase 質問が出る前
に誤適用か語彙 gap を surface する。
