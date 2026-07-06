言語: [English](case-studies.md) | 日本語

# Case Studies — 実践での Quadrant 振り分け

> **目的。** よくある業務 AI を象限に振り分けた具体例 (適用 ADR、
> アーキテクチャ、典型的 phase 配置を含む) を提示する。下記の case は
> *illustrative*: チームの思考刺激のためで、case 名から pattern-match
> するのは推奨しない。Phase ≠ Quadrant: 同じ Quadrant が design / operation
> 両 phase に出現しうる; 各 case は *typical* な配置を記すのであって
> *exclusive* ではない。
>
> **時効性。** 各 case には publication date が付く。記載は当該日時点
> での LLM / tooling capability を前提とする。Capability が変わったら
> 再評価する。

## (Autonomous Agentic Loop Quadrant、*Typical placement: Design phase*) コーディングエージェント

*Published 2026-05-01。*

- **業務。** 開発者がコーディングエージェントに repository 内の
  調査、修正、テスト、コミットを依頼する。各 session はタスク完了で
  終わる。
- **Phase。** Design phase resident。エージェントが "always on"
  (Devin、GitHub Copilot Coding Agent in CI/CD) でも、各 session は
  独立した *design session* で、その探索が完結したら終わる; state は
  operation-relevant な形で session 間に蓄積されない。
- **Quadrant。** Autonomous Agentic Loop Quadrant。
  - Q0 (phase?) — Design。
  - Q1 (deterministic?) — No。
  - Q3 (workflow definable?) — No; 次の action は前の file 読み取りや
    test 結果次第。
  - Q4 (gap-bearer + Phase-crossing) — 両方答える必要。Gap-bearer は
    通常 session を起こした developer; 無人 CI run の場合は repository
    owner。Phase-crossing decision は自動的 (これが design phase
    そのもの)。
- **アーキテクチャ。** Controlled tool set (file read, file edit,
  shell, test runner) と explicit termination (task complete /
  approval gate) を持つ ReAct-style autonomous loop。
- **適用 ADR。** 全 10 本。ADRs 0005, 0006, 0008, 0009 が weight; ADR-0010
  の Phase-crossing 条件は自動 satisfied。
- **これが ADR-0010 の例外でない理由。** コーディング session は
  独立した design session の sequence。Continuous availability は
  deployment-level の事実で、phase-level の事実ではない。コーディング
  エージェントを operation-phase deployment として扱うのが ADR-0010 が
  指摘する category error。

## (Autonomous Agentic Loop Quadrant、*Typical placement: Design phase*) Ad-hoc 分析の Deep Research

*Published 2026-05-01。*

- **業務。** ユーザがリサーチ問題を submit; 答えるには bounded されない
  knowledge corpus 上での反復検索、retrieval、synthesis が要る。各
  query は synthesis 完了で終わる。
- **Phase。** Design phase resident (per-query design session)。
- **Quadrant。** Autonomous Agentic Loop Quadrant。
- **アーキテクチャ。** Retrieval tool (search, fetch, cite) と
  synthesis ステップを持つ ReAct-style autonomous loop。
- **適用 ADR。** 全 10 本。Session レベルの gap-bearer はユーザ (有償
  deployment ではユーザの employer); Phase-crossing decision は自動。
- **これが operation-phase でない理由。** Query session は探索的;
  次の検索は前の結果次第。Deployment が operation-phase に *見える*
  (サービスが常時利用可能) のは delivery property で、phase property
  ではない。

## (LLM Workflow Quadrant **batch sub-form**、*Typical placement: Operation phase*) FAQ 分類

*Published 2026-04-30。*

- **Sub-form。** Batch sub-form: パイプラインが制御フローを所有し、
  LLM は固定された役割 (`classify(question) -> category`) で各質問
  あたり 1 回呼び出される。
- **業務。** ユーザが質問を submit; システムは N 個の FAQ カテゴリの
  いずれかに分類し、対応する答えを返す。
- **Phase。** Operation phase。Default composition (Phase-crossing
  decision 不要; Quadrant 4 は composition に入らない)。
- **Quadrant。** LLM Workflow Quadrant。
  - Q1 (deterministic?) — No、意味判断要。
  - Q3 (workflow definable?) — Yes; 1 分類呼び出し、1 lookup、1 return。
- **アーキテクチャ。** Single-purpose LLM 関数: documented schema を
  持つ `classify(question) -> category`。続いて決定論的 lookup
  `category -> answer`。Autonomous loop なし。
- **適用 ADR。** 0001, 0003, 0006, 0007 ([`governance-mapping.ja.md`](governance-mapping.ja.md)
  の full list 参照)。
- **Redirect 経路。** 分類が誤った場合、`classify()` の role owner
  (model 選定、prompt 設計) が redirect 先。
- **Anti-pattern。** 「FAQ をどれにするか LLM が決める」自律 agent
  として実装すること。
  [`anti-patterns.ja.md#bounded-work-on-autonomous-loop`](anti-patterns.ja.md)
  参照。

## (LLM Workflow Quadrant **conversational sub-form**、*Typical placement: Operation phase*) カスタマーサポート専門 chat

*Published 2026-04-30。*

- **Sub-form。** Conversational sub-form: 会話中の人間が *判断主体
  (judging agent)*。各 turn は周囲ハーネスで役割が固定された bounded
  LLM 呼び出し (system prompt + retrieval + history) である。
- **業務。** Product knowledge base に grounded された multi-turn chat。
  Scope 外の topic に到達したら人間に escalate する。
- **Phase。** Operation phase。Default composition。
- **Quadrant。** LLM Workflow Quadrant (multi-turn だが bounded —
  下記 boundary 議論)。
  - Q1 — No。
  - Q3 — Yes: 各 turn は固定 role の bounded LLM 呼び出し (system
    prompt + retrieval + history) + escalation check。
- **アーキテクチャ。** 専門 chat agent: 1 turn = 固定 role を持つ
  単一 LLM 呼び出し (retrieve した context を使って product X の
  質問に答える)。Escalation check 自身も bounded 分類呼び出し。
- **適用 ADR。** 0001, 0003 (RAG content は untrusted), 0006, 0007。
- **Redirect 経路。** 誤った答え → answer 呼び出しの role owner
  (誤った doc が retrieve された場合は RAG curator; prompt が誤った
  synthesis を促した場合は prompt designer)。
- **境界注記。** Multi-turn chat agent は (3) と (4) の境界に座る —
  ADR-0009 の open question 1 参照。テスト: 不通常な user input 下で
  各 turn の LLM 呼び出し role が bounded のままか、それとも agent が
  「次に何をするか」を product X 質問の答えを越えて決め始めるか?
  Role の bound が hold すれば (3)、hold しなければ (4) に流れて
  いて ADR-0009 triage が要る。

## (LLM Workflow Quadrant **batch sub-form**、*Typical placement: Operation phase*) 請求書マッチング

*Published 2026-04-30。Source: 2026-04-29 essay。*

- **Sub-form。** Batch sub-form: パイプライン (`process_invoice()`)
  が制御フローを所有し、1 つの semantic-judgment ポイントで
  single-purpose LLM function (`match_line_items(...) -> Verdict`)
  が呼ばれる。
- **業務。** 入庫した請求書を発注書とマッチして承認 / escalate /
  reject する。
- **Phase。** Operation phase。Default composition。
- **Quadrant。** LLM Workflow Quadrant。
  - Q1 — Mostly yes (PO 存在、期日、重複、金額完全一致) だが、line
    item マッチには意味判断が要る。
  - Q3 — Yes; 制御フローは決定論パイプラインで line-item-matching
    点に LLM 呼び出しを 1 つ持つ。
- **アーキテクチャ。** 決定論パイプライン (元 essay の
  `process_invoice()`) と single-purpose LLM 関数
  `match_line_items(invoice_lines, po_lines) -> Verdict`。
- **適用 ADR。** 0001, 0003, 0005 (matching prompt/schema 変更は
  approval を通る), 0006, 0007。
- **Redirect 経路。** 誤った match は LLM 関数の role owner か
  pipeline logic owner のどちらかに切り分けられる。両方とも
  identifiable。

## (Autonomous Agentic Loop Quadrant、*Typical placement: Design phase*) Open-ended 文献探索のリサーチアシスタント

*Published 2026-04-30; phase 注記 2026-05-01 追加。*

- **業務。** ユーザがリサーチ質問を submit; 答えるには事前列挙でき
  ない knowledge corpus 構造の中で反復検索、読解、synthesis が要る。
- **Phase。** Design phase resident (per-query design session;
  上の Deep Research case と同種だが、consumer-tool variant ではなく
  team-deployed variant を扱うため別記)。
- **Quadrant。** Autonomous Agentic Loop Quadrant。
  - Q0 — Design (per query)。
  - Q1 — No。
  - Q3 — No; 次の検索は前の検索結果次第。
- **アーキテクチャ。** Controlled tool set (search, retrieve, cite)
  と explicit termination を持つ ReAct-style autonomous loop。
- **適用 ADR。** 全 10 本。ADRs 0005, 0006, 0008, 0009 が load-bearing;
  ADR-0010 の Phase-crossing decision は自動 satisfied (design phase)。
- **Deployment 時に required な commitments。**
  - **ADR-0009.** Triage decision を記録; 業務を Autonomous Agentic
    Loop Quadrant 業務として明示。
  - **ADR-0008.** Accountable operator を指名。*Alice がこのリサーチ
    アシスタント deployment の accountable operator*。
  - **Pre-named gap-bearer。** 同じ operator (個人権限を超える業務なら
    上位の role)。
  - **ADR-0005.** Behavior-modifying write (loop の prompt、tool、
    終了条件への変更) が Alice の approval gate を通る。加えて
    deployment 固有の選択として — ADR の floor より厳しい設定 —
    Alice は outward-facing な action (report 公開、email 送信、
    channel 投稿) も gate する。
- **Redirect 経路。** 部品レベル redirect は閉ざされる。Report が
  誤っていたら Alice が redirect 先 (gap-bearer commitment 通り)。
  Post-incident options: 再訓練、re-architect (一部業務を LLM
  Workflow Quadrant に移す)、retire。

## (Algorithmic Search Quadrant、*Typical placement: Operation phase*) リソーススケジューリング

*Published 2026-04-30。*

- **業務。** 制約付きで有限のリソース (部屋、機械、人員) を有限の
  demand に割り当てる。
- **Phase。** Operation phase。
- **Quadrant。** Algorithmic Search Quadrant。
  - Q1 — Yes; 制約と目的関数は完全 specifiable。
  - Q2 — No; 組合せ列挙が要る。
- **アーキテクチャ。** 制約ソルバ、整数計画、metaheuristic search。
  LLM は loop に入らない。
- **AAP の射程外。** 標準的 SE アカウンタビリティが適用; algorithm
  author か制約仕様 owner が redirect 先。
- **Anti-pattern。** 「LLM にスケジュールを聞く」自律 agent として
  実装すること。
  [`anti-patterns.ja.md#or-problem-on-autonomous-loop`](anti-patterns.ja.md)
  参照。

## (Hybrid) 契約レビュー

*Published 2026-04-30。*

- **業務。** 契約を review: 条項抽出、policy library との照合、
  novel-pattern 条項を人間注意のために flag する。
- **Quadrant。** Hybrid。
  - **条項抽出** — LLM Workflow Quadrant。各条項型は documented
    schema を持つ bounded 呼び出し。
  - **Policy 比較** — LLM Workflow Quadrant。各 policy check は
    bounded 呼び出し。
  - **Novel-clause 分析** — Autonomous Agentic Loop Quadrant、ただし
    分析が本当に open-ended な判例探索を要する場合 *のみ*。実務上は
    novel-clause 分析は autonomous loop ではなく人間に渡されることが
    多い (hallucinated 判例 citation のコストが高いため)。
- **アーキテクチャ。** LLM Workflow Quadrant 呼び出しの pipeline +
  novel-clause 部分への approval-gated escalation (人間か、ADR-0008/0009
  を満たした Autonomous Agentic Loop)。
- **LLM Workflow 部分の適用 ADR。** 0001, 0003, 0005, 0006, 0007。
- **Autonomous Agentic Loop 部分の適用 ADR (使う場合)。** 全 10 本。
- **境界の規律。** 両部分間の handoff は構造的選択として log。
  Autonomous Agentic Loop 部分は workflow operator とは別の named
  gap-bearer を持つ (役割分離が重要なら)。

## (Autonomous Agentic Loop Quadrant、operator 経験留保付き) ナレッジベース copilot

*Published 2026-04-30。Source: 2026-04-29 essay の実装経験引用。*

- **業務。** 公式ドキュメント大量。ユーザの質問の答えには事前計画
  できない方法で knowledge graph traversal が要る。
- **Phase。** Per-query Design phase。Continuous availability は
  delivery property。
- **Quadrant。** Autonomous Agentic Loop Quadrant。
  - Q3 — No。次の検索は observation 次第。Bounded だと業務遂行を
    妨げる。
- **アーキテクチャ。** Retrieval tool を持つ ReAct-style loop。
- **適用 ADR。** 全 10 本。
- **報告された operator 経験。** 業務自体には強力な結果; 運用での
  予測可能性は低下。Iteration 数や loop の経路は run 間でばらつく。
  これはこの象限の本質的な性質で、tooling 限界ではない。計画に織り
  込む。
- **Required commitments。** ADR-0008 named operator + ADR-0009 named
  gap-bearer。ADR-0005 approval gate は behavior-modifying write に
  かかる; 構築された各答えの external publication まで gate するかは
  ADR の floor を超える deployment 判断 (各答えを approval-gated
  artifact として扱うか、deployment envelope を approval-gated にして
  答えを stream するか)。

## (Skill-design gradient case) 同じジョブ、別 phase、別形態

*Published 2026-05-02。Source: 2026-05-02 essay (essay 7)。*

- **業務。** 自然言語・構造化テキストの artifact 集合から繰り返し現れる
  パターンを抽出し、再利用可能な固定単位 (rule、template、embedding、
  分類 schema) として固める。同じジョブが lifecycle の異なる時点で
  recurring に発生する — 探索的 authoring の最中に 1 度、artifact 集合
  が settle した後に 1 度。
- **同じジョブ、2 つの phase、2 つの形態。** ジョブの
  [skill-design gradient](../glossary.ja.md#skill-design-gradientskill-設計勾配)
  上の位置は phase で変わるのであって model capability で変わるのでは
  ない。同じジョブの 2 実装が legitimately に併存する:
  - **Design phase 形態。** LLM が runtime で invoke する自然言語 skill。
    対象 codebase、IDE、関連 file type、実行環境が呼び出しごとに変わり、
    固定 path と命名規約は事前に仮定できない。形態としては Autonomous
    Agentic Loop Quadrant 寄り (呼び出しごとに runtime 判断) だが、
    自由形式 ReAct loop ではなく単一 skill のまま。
    [Target identifiability](../glossary.ja.md#target-identifiability識別可能性)
    が低い;
    [scale-resilience](../glossary.ja.md#scale-resilience-スケール耐性)
    は規模が小さいため制約にならない。
  - **Operation phase 形態。** 対象が固定 directory layout と安定した
    命名規約に乗った瞬間、判断は決定論的 pipeline として encode される:
    embedding + clustering + 閾値ロジック、避けられない場所だけ単一の
    bounded LLM 呼び出し。形態は LLM Workflow Quadrant (Quadrant 3)、
    LLM 呼び出しが完全に消える場合は Script Quadrant (Quadrant 1)。
    Target identifiability が高い; scale-resilience は重要、operation
    形態は呼び出しごとの slice ではなく settled corpus 全体に対して
    走るため。
- **Phase placement。** 2 形態は同一プロジェクトの異なる lifecycle
  phase に住む。両方が併存しうる; どちらも他方の一般化ではない。
- **各形態の Quadrant routing。**
  - Design phase 形態 — Quadrant 4 寄りの skill だが、design session
    内で invoke されるため、ADR-0010 (1) Phase-crossing decision は
    自動的に満たされる (これが design phase そのもの)。
    [`anti-patterns.ja.md`](anti-patterns.ja.md#skill-内-phase-descent-を無視する--一律-react-化または一律-freeze-化)
    の「Skill 内 phase descent を無視する」entry が、対象が settle
    する前にこの形態を frozen pipeline に押し込むことを防ぐ。
  - Operation phase 形態 — default operation composition に入る
    Quadrant 3 (または Quadrant 1) component。形態がもはや Quadrant 4
    ではないため Phase-crossing decision は不要。
- **なぜ capability が lever でないか。** より高 capability の LLM は
  design-phase 形態に固定対象を与えないし、operation-phase 形態に
  novel な未見 variation への runtime 許容度を与えない。AKC の
  「capability ↑ → holistic judgment OK」原則はどちらの二次的な力も
  カバーしない。どの形態が構造的に正直かを決めるのは Phase。
- **単一 skill 内の再帰。** 同じ gradient が単一 skill *内部* にも
  適用される: design-phase skill が固定 lookup の subcomponent (target
  identifiability が局所的に高い — 例えば既知 path の config file) と
  runtime LLM 判断の subcomponent を併せ持ちうる。診断フレームは
  再帰的に歩く。
- **Lineage note。** この case の背後の具体例は 2026-05-02 essay にある;
  navigator は harness-neutral なため、本 navigator では named しない。
  Essay は同じジョブの design-phase / operation-phase split を呈する
  AAP-author の 2 skill を歩く。

## これらの case の位置づけ

上記の case は *illustrative routing* であり、推奨 reference
architecture ではない。本 repository は「reference architecture」
ファイルを維持しない — reference architecture は背景の routing
judgment より早く溶けて消えるから。象限が identify されたら、
アーキテクチャは template ではなく象限の性質 (post-hoc separability、
named gap-bearer commitment) と適用 ADR から導かれる。
