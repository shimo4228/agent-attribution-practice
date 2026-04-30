言語: [English](anti-patterns.md) | 日本語

# Anti-patterns — Quadrant 選択のよくある失敗

> **目的。** 業務 AI 配備が業務を誤った象限に振り分ける recurring な
> 様式と、その回復方法を記録する。各 anti-pattern は失敗モード、
> なぜ起きるか、何が壊れるか、[`decision-tree.ja.md`](decision-tree.ja.md)
> でどう回復するかを示す。

## Phase-crossing decision なしの operation-phase autonomous loop

> 「自律的な research / triage / 分析エージェントを production
> workflow に deploy した。新パターン? 動的に handle する。」

- **失敗モード。** Autonomous Agentic Loop Quadrant 部品が operation
  phase に default route として置かれているのに、deployment 時に明示
  Phase-crossing decision が記録されていない。ADR-0010 が surface する
  (ban ではなく) configuration — 意識的に選ぶべきで、滑り込ませるもの
  ではない。
- **なぜ起きる。** 「自律エージェントが業務 workflow を handle」が
  vendor の支配的 framing。チームはこの framing を default として
  cost を named せずに受け入れる。Operation 中に現れる新パターンが
  silently に loop に absorbed され、design-phase feedback として
  surface しない。
- **何が壊れる。** [`attribution gap`](../glossary.ja.md#attribution-gap)
  が production runtime 内で発火する。動的 absorbed された各新パターンが
  attribution の blend をさらに進め、部品レベル redirect が foreclose
  される *かつ* deployment record にこの configuration を選んだ理由が
  記載されていない。Post-incident review が autonomous loop を運用
  業務で動かしていたが gap-bearer は named (ADR-0009 通り) で
  Phase-crossing decision は未記録の状態を発見する — *moral crumple
  zone* 文献が記す失敗モード。
- **回復。** [`decision tree`](decision-tree.ja.md) の Q0 + Q4 を
  明示実行する。業務が本当に Quadrant 4 を operation で要求するなら、
  Phase-crossing decision を記録: *「新パターンを design に feedback
  として戻す」* または *「loop in place で handle、recurring
  attribution gap を引き受ける」*。両方とも admissible。どちらも
  acceptable でなければ業務は Q3 (LLM Workflow Quadrant) に属し、
  autonomous loop は operation surface から除去すべき。

## 運用パターンを design に戻さず in place で吸収

> 「先週 edge case にぶつかった — agent が figure out した。」

- **失敗モード。** 運用に出現した新パターンを autonomous loop の
  runtime が absorb する形で扱う (design-phase feedback として
  surface しない)。ADR-0010 は明示的 routing-back-to-design path を
  *recommended* response として位置づける; この anti-pattern は代替を
  judgment ではなく default として選んでいる。
- **なぜ起きる。** Autonomous loop は novel case を「とりあえず
  handle」する見え方になる。それがアーキテクチャの目的でもある。
  問題は、これが本来 surface されるべき upstream な fix の design
  feedback を mask すること。
- **何が壊れる。** 三層診断の Layer 3 (phase 混同): design と operation
  が実務上区別できなくなる。チームが design phase を別 activity として
  維持しなくなる。「もう runtime が handle した」ため将来の workflow
  改善が起きなくなる。
- **回復。** Routing-back-to-design path を明示し log する。Operation
  で surface した新パターンに tag を打ち、design-phase review に
  escalate、既存 workflow に encode (LLM Workflow Quadrant) するか、
  in-place handling を legitimately 要する domain として accept (record)
  する。

## Bounded 業務に autonomous loop

> 「カスタマーサポート / FAQ 分類 / 請求書マッチングを ReAct エージェント
> として実装する。」

- **失敗モード。** LLM Workflow Quadrant 業務が autonomous-loop
  アーキテクチャに routing される。記事 5 (2026-04-30) の中心診断。
- **なぜ起きる。** 業界の語彙に LLM Workflow Quadrant の肯定形の
  名前が無い。「決定論的でないもの」が消去法で「agent」に mapping
  される。Marketing と tooling が autonomous-loop default を補強。
- **何が壊れる。** 機能すべき部品レベル redirect が機能しなくなる。
  各 LLM 呼び出しの役割が runtime で決まるので、失敗を特定の部品に
  isolate できない。チームは誤った象限のアカウンタビリティ性質を
  仮定する — redirect が可能だと assume するが、すでに foreclose
  されている。
- **症状。** Sandbox 強度がエスカレートする。Human-in-the-loop の負荷
  が増える (operator が system が redirect できない判断を absorb)。
  Postmortem が「agent が weird なことをした」で終わり、特定部品を
  named できない。
- **回復。** Decision tree を再実行、特に Q3。業務に definable な
  workflow がある; その形で構築する。Autonomous loop を決定論制御
  フロー + bounded LLM 呼び出し (1 呼び出し 1 役割、documented schema)
  に置き換える。LLM Workflow Quadrant の ADR を適用。

## OR 問題に autonomous loop

> 「スケジューリング / ルーティング / リソース割当を LLM agent として
> 実装する。」

- **失敗モード。** Algorithmic Search Quadrant 業務が autonomous-loop
  アーキテクチャに routing される。Category error。
- **なぜ起きる。** 「これに AI が要る」が「LLM が要る」と読まれ、
  古典アルゴリズムが解いてきた組合せ探索業務が loop に押し込まれる。
- **何が壊れる。** 解の品質が古典探索より悪化。Loop が feasibility を
  hallucinate する。Objective function が無く、LLM が drift する
  暗黙ゴールに最適化する。さらに悪いことに、これら失敗が Autonomous
  Agentic Loop Quadrant の attribution gap を継承する。
- **回復。** Q1 (deterministic?) → Yes。Q2 (search space static and
  finite?) → No。Algorithmic Search Quadrant。古典ソルバを使う。LLM
  は upstream (intent 解析、構造化問題インスタンス生成) で役割が
  あるかもしれないが、search 自体は古典アルゴリズムに属する。

## 決定論的タスクに autonomous loop

> 「フォーム入力 / データ正規化を agent として実装する。」

- **失敗モード。** Script Quadrant 業務が autonomous-loop アーキテクチャ
  に routing される。
- **なぜ起きる。** 「すべてを agent 化」のピッチが autonomous loop を
  万能溶剤として位置づける。古典スクリプトがミリ秒で handle する業務
  が秒オーダで動きコストの高い loop として再実装される。
- **何が壊れる。** Cost、レイテンシ、信頼性すべてが利益なしで劣化する。
  決定性が失われる (loop が run 間で異なる出力を produce)。Audit が
  置き換え対象のスクリプトより難しくなる。
- **回復。** Q1 → Yes。Q2 → Yes。Script Quadrant。スクリプトか
  パイプラインを使う。

## 高 risk domain に Ungoverned autonomous loop

> 「自律 research / 法務 / 医療 / 金融 agent を Q4 に答えずに deploy
> する。」

- **失敗モード。** Autonomous Agentic Loop Quadrant 業務が pre-named
  gap-bearer なしで動いている。ADR-0009 が防ぐべき configuration。
- **なぜ起きる。** Triage decision が configuration として扱われる
  (structural commitment ではなく)。「Post-incident に責任を figure
  out する」が暗黙の (禁止された) 計画。
- **何が壊れる。** *Moral crumple zone* 失敗 (Elish 2019)。何かが
  起きたとき、責任は operationally proximate な人間 — 通常 loop の
  runtime 判断に対する control が最も限定的な operator — に着地する;
  Gap を引き受けると commit した party ではない。
- **回復。** Q4 を明示実行。Organizationally identifiable gap-bearer
  を named (ADR-0008 + ADR-0009 通り) するか、*deploy しない*。業務が
  本当に Autonomous Agentic Loop Quadrant を要求し gap-bearer が named
  できない場合、deployment は infeasible。

## Approval gate を runtime checkbox にする

> 「Human Approval Gate がある — ユーザが各 action 前に "OK" をクリック
> する。」

- **失敗モード。** ADR-0005 が ADR-0001 critique の意味での signpost
  に縮小。
- **なぜ起きる。** Approval gate が confirmation dialog と confused
  される。後者は habituate するが、前者は named human による inspect
  と decide が要る。
- **何が壊れる。** Operator がクリック through する。人間の decision
  で終端すべき chain が「user が Enter を押した」で終端する。Post-incident
  review が実 sign-off を発見できない。
- **回復。** ADR-0005 は approval が *behavior-modifying changes* に
  対するもので各 runtime action ではないこと、approver は *named
  human* であること、gate は disable できないことを規定する。
  Behavior-modifying changes (prompt、schema、role 定義、routing logic
  の変更) が正しい granularity。Runtime confirmation は別 mechanism
  に属する (必要なら)。

## Audit log を sample にする

> 「agent run の sample を review のために log する。」

- **失敗モード。** ADR-0006 が確率的 observability practice に縮小。
  Causal traceability は *すべての* event が再構成可能であることを
  要求する。
- **なぜ起きる。** Logging cost の懸念; 「全部読む人いる?」 Audit log
  が monitoring data として扱われ、ADR-0006 が named する structural
  artifact ではなくなる。
- **何が壊れる。** Incident が起きたとき、関連 run が sample に無い。
  実際に重要な case で再構成が失敗する。Sample-based logging は
  monitoring であって audit ではない。
- **回復。** すべての event に append-only logging。Storage cost は
  post-incident reconstruction 失敗 cost と相殺で支払う; 後者は
  deployment が Autonomous Agentic Loop Quadrant にあるとき遥かに
  高い。

## Quadrant を agent type として扱う

> 「LLM Workflow Quadrant agent / ReAct agent を構築する。」

- **失敗モード。** Quadrant を業務カテゴリではなく agent カテゴリ
  として扱う。
- **なぜ起きる。** Vendor のピッチ (「弊社の autonomous agent は…」)
  と product framing (「the X agent」) が agent を typed entity として
  考えることを encourage する。
- **何が壊れる。** 同じ agent process が異なる象限の業務に deploy
  されうる。Agent を象限に lock すると triage が hide する。Agent が
  異なる象限の業務を依頼されたとき、アーキテクチャ mismatch が
  invisible になる。
- **回復。** Quadrants は *業務* を describe する。Agent が依頼される
  各業務 piece に decision tree を実行する。同じ agent process でも
  異なる象限は異なる適用 ADR set を要求する。

## 「明らかに agent 必要」で triage をスキップ

> 「明らかに自律タスクだ — loop を build しよう。」

- **失敗モード。** Q3 が実際には answer されていない。「これは agent
  task」という直感が workflow-definability check を置き換える。
- **なぜ起きる。** Cognitive shortcut。答えが obvious に見えるとき
  triage が bureaucratic に感じる。
- **何が壊れる。** 「明らかに agent」業務の半分は、Q3 を明示実行する
  と workflow として definable と判明する。Triage をスキップすると
  autonomous loop が premature に install され、attribution-gap cost
  を必要なしに支払う。
- **回復。** undefinable と結論する **前** に、Q3 を必ず answer する
  — workflow をホワイトボードに描けるか? 描けるなら業務は LLM Workflow
  Quadrant にある。

## Common thread

これらの anti-pattern の多くが構造を共有する: より良い architectural
fit を持つ業務 piece が、現行 discourse で salient な default が
"worse" fit のため、worse fit に routing される。Decision tree は
その salience を中断するために存在する — 5 質問を明示歩めば、より
良い fit が surface することが多い。
