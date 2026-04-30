言語: [English](README.md) | 日本語

# Business AI Quadrants — Adoption Navigator

> **このディレクトリの位置づけ。** 業務を accountability distribution
> （責任の分配）を保つアーキテクチャに振り分けるための実務 navigator。
> 10 本の ADR と 4 象限が AAP の **二軸構造** を成す: ADR は問いに対
> する応答 (*何を制約すべきか、誰が責任を負うか*)、Quadrants は問いを
> 切る軸 (どのアーキテクチャに業務を流せばよいか) を提供する。
>
> **Phase は Quadrant と独立。** 各 Quadrant は design phase / operation
> phase の両方に出現しうる。ADR-0010 は operation phase × Quadrant 4 の
> 配置に対する narrow な手続きルールを差し出すもので、Quadrant を Phase
> で分割するものではない。

4 象限は zenn 記事 4 (2026-04-29) で導入され、5 (2026-04-30) で
attribution の構造が、6 (2026-05-01) で Phase の区別が加わった。本
repository では essay 4 の暫定名 (Classical AI Quadrant / ReAct
Quadrant) を harness-neutral 名 (Algorithmic Search Quadrant /
Autonomous Agentic Loop Quadrant) に改名している。旧名は alias として
[`../glossary.ja.md`](../glossary.ja.md) に残す。

## 2 つの軸

| | ワークフロー定義可 | 探索的 |
|---|---|---|
| **決定論で書ける** | (1) Script Quadrant | (2) Algorithmic Search Quadrant |
| **意味判断が必要** | (3) LLM Workflow Quadrant | (4) Autonomous Agentic Loop Quadrant |

- **横軸** — 業務を決定論ルールで書けるか、それとも LLM の意味判断が
  必要か
- **縦軸** — 業務の制御フローが事前定義可能か (次の手順がコードで
  決まる)、それとも探索的か (次の手順を runtime で観察に基づいて
  決める必要がある)

縦軸は Anthropic "Building Effective Agents" の "predetermined code
paths vs LLM dynamically directs its own process" 区別と直接対応する。

## 4 つの象限

### (1) Script Quadrant

決定論 × 定義可。LLM を使わないスクリプトとパイプライン。フォーム
入力、データ正規化、ルックアップ、検証。AAP の 10 本の ADR は概ね
範囲外で、古典的なソフトウェア工学のアカウンタビリティが適用される。

### (2) Algorithmic Search Quadrant

決定論 × 探索的。古典的探索、動的計画法、MCTS、強化学習。配送ルート
最適化、スケジューリング、組合せ割当。本 repository の LLM 中心の
ADR の射程外で、関心事はアルゴリズム正当性、探索空間カバレッジ、
標準的な SE アカウンタビリティ。

### (3) LLM Workflow Quadrant

意味判断 × 定義可。決定論的制御フロー + 役割を明示した bounded LLM
呼び出し (各呼び出しが documented role と input/output schema を持つ)。
各 LLM 呼び出しの寄与は **事後分離可能** で、redirect が機能する。
Anthropic "Building Effective Agents" の workflow patterns (prompt
chaining, routing, parallelization, orchestrator-workers,
evaluator-optimizer) や OpenAI "A Practical Guide to Building Agents"
が扱う領域と連続する。

現行 LLM 応用の大半が default として収まる象限。記事 5 (2026-04-30)
は、業界の語彙にこの象限を肯定形で名指す語がないため、消去法で (4)
に流される構造を診断している。

### (4) Autonomous Agentic Loop Quadrant

意味判断 × 探索的。LLM が runtime で次の各行動を決めるアーキテクチャ
— "ReAct" パターン (Yao et al. 2022) とその子孫 (Deep Research、
探索的コーディングエージェント、open-ended ブラウジング)。業務が
本当に open-ended な探索を要求し、LLM 呼び出しの役割を bounded に
することが業務の遂行を妨げる場合に正当に必要となる。

この象限を選ぶことは、deploy する組織に **除去できない attribution
gap（寄与の事後分離不能ギャップ）** を引き受ける commitment を負わせる:
runtime で判断要素がブレンドされるため事後分離可能性が閉ざされ、
redirect が分離可能な寄与のレベルでは機能しない。ADR-0009
[Triage Before Autonomy](../adr/0009-triage-before-autonomy.ja.md) と
[`../glossary.ja.md#attribution-gap寄与の事後分離不能ギャップ`](../glossary.ja.md)
を参照。

## このディレクトリの使い方

順序:

1. [`decision-tree.ja.md`](decision-tree.ja.md) — 5 質問の triage で
   業務を象限と適用 ADR set に振り分ける
2. [`governance-mapping.ja.md`](governance-mapping.ja.md) — 各象限の
   適用 ADR、必要 controls、失敗時の redirect 経路を dense table で
3. [`case-studies.ja.md`](case-studies.ja.md) — 具体例 (FAQ 分類、
   カスタマーサポートチャット、請求書マッチング、リサーチアシスタント、
   スケジューリング、契約レビュー、ナレッジベース copilot、コーディング
   エージェント、Deep Research) を象限 + Phase + アーキテクチャに振り
   分けたもの
4. [`anti-patterns.ja.md`](anti-patterns.ja.md) — よくある象限選択
   ミス、なぜ起きるか、どう回復するか

## 二軸構造 — ADR と Quadrants

両者は別の問いに答える:

- **10 本の ADR** は「何を制約すべきか、誰が責任を負うか」に答える
  — *accountability distribution が成立する象限で運用しているという
  前提のもとで*。
- **4 つの象限** は「この業務に accountability distribution が
  そもそも成立するか、するならどのアーキテクチャ regime で?」に
  答える。

二軸の姿勢は AAP の「*10 の判断、固定された framework ではない*」
姿勢を保つ: Quadrants は routing のための診断フレームであり、
prescriptive な taxonomy ではない。象限ごとに ADR set の **subset**
が違う load-bearing 重みで適用される。ADR set 自体は象限で partition
されない。

### Phase は第 3 の dimension (partition ではない)

Phase (design / operation) は Quadrant と独立。各象限は両 phase に
出現しうる: 設計中に prototype された LLM Workflow が運用に promote
される、autonomous loop で domain を探索 (design) してから知見を
workflow に組み込む (operation)、いずれも Quadrant の境界を超えるが
Phase はその両側で動く。

ADR-0010 が差し出すのは特定の placement 1 つ — **operation phase ×
Autonomous Agentic Loop Quadrant** に対して、運用中に新パターンが
現れた時に動的に loop で吸収するか design phase に feedback として
戻すかの **Phase-crossing decision（フェーズ越境判定）** を deployment
時に明示することを要求する。Procedural rule であって architectural
partition ではない。

## Status

この層は AAP の active な publication の一部で、adoption 経験の蓄積に
合わせて更新する。Quadrant 境界 (とくに multi-turn chat agent が LLM
Workflow Quadrant に収まるか Autonomous Agentic Loop Quadrant に渡るか)
の語彙はまだ詰めている途中; ADR-0009 の open questions を参照。
