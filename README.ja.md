言語: [English](README.md) | 日本語

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652014.svg)](https://doi.org/10.5281/zenodo.19652014)

> **Agent Attribution Practice (AAP)** — 10 の判断と 4 つの Business AI
> Quadrants（業務 AI 四象限）、固定された framework ではない。

自律 AI エージェントにおける **attribution** (誰が書いたか、誰が原因か、
誰が事後に辿れるか) の分配に関する 10 の判断と、その分配が成り立つ
アーキテクチャに業務を振り分ける 4 象限の診断フレーム。
[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)
の実装・運用の摩擦から発見されたものであり、トップダウンの設計判断ではない。

## なぜこの repo が存在するか

現状の AI ガバナンスは **看板フェーズ** にいる。システムプロンプトに
「X するな」と書き、倫理ガイドラインを PDF で公開し、安全委員会を設置
する。執行のないテキスト。登れる壁に立てた看板。

歴史的に効いてきたのは看板ではなく **構造としてのアカウンタビリティ**
だ。実装されていない capability は呼び出せない。blast radius は設計で
限定される。行動を変える変更は必ず名前のある人間のサインオフを通る。
事故後には因果を遡行できる。人間社会はこのパターンを 3 世紀かけて
磨いた。エージェントも同じパターンを採るか、さもなくば説明責任の
切れた層になる。

この repo の 10 本の ADR は「**何を制約すべきか、誰が責任を負うか**」
という形の判断を記録する。フレームワークから演繹したものではなく、
実装から抽出したものだ。

## 10 の判断

| ADR | 原則 | Status |
|-----|------|--------|
| [0001](docs/adr/0001-security-by-absence.ja.md) | Security by Absence — 危険な capability は制限するのではなく実装しない | accepted |
| [0002](docs/adr/0002-deterministic-prohibition-at-scaffolding.ja.md) | Deterministic Prohibition at the Scaffolding Layer — 不在が取れない禁止は、モデル重みでなく scaffolding（足場）側で決定論的に遮断する | accepted |
| [0003](docs/adr/0003-untrusted-content-boundary.ja.md) | Untrusted Content Boundary — 蓄積メモリが権威を与えない | accepted |
| [0004](docs/adr/0004-single-external-adapter.ja.md) | Single External Adapter — blast radius を構造で限定する | accepted |
| [0005](docs/adr/0005-human-approval-gate.ja.md) | Human Approval Gate — 行動変更は名前のある人間の承認を要する | accepted |
| [0006](docs/adr/0006-causal-traceability.ja.md) | Causal Traceability — あらゆるイベントが事後に再構成可能 | accepted |
| [0007](docs/adr/0007-scaffolding-visibility.ja.md) | Scaffolding Visibility — 振る舞いは不透明な weight ではなくファイルに存在する | accepted |
| [0008](docs/adr/0008-one-agent-one-human.ja.md) | One Agent, One Human — アカウンタビリティチェーンは名前のある個人で終端する | **experimental** |
| [0009](docs/adr/0009-triage-before-autonomy.ja.md) | Triage Before Autonomy — autonomous-loop アーキテクチャの採用は除去できない attribution gap（寄与の事後分離不能ギャップ）を引き受ける commitment になる | **experimental** |
| [0010](docs/adr/0010-phase-separation.ja.md) | Phase Separation Between Design and Operation — Autonomous Agentic Loop Quadrant を operation phase に置く場合は Phase-crossing decision（フェーズ越境判定）を deployment 時に明示 | **experimental** |

最初の 3 本は **禁止強度の階層** を成す: absence > scaffolding
enforcement > untrusted boundary。次の 2 本 (0004, 0005) が構造的
トポロジと human-in-the-loop を加える。中央の 2 本 (0006, 0007) は
それら制約が要求する artifact — 辿るべき記録と、検査可能な scaffolding。
8 本目は人間側の終端。ADRs 0009 と 0010 が **triage pair**: 0009 が
problem-space triage (どの Quadrant か)、0010 が時間軸の triage
(operation phase に置いた場合の Phase-crossing decision)。Phase と
Quadrant は独立な dimension で、ADR-0010 は procedural な rule で
あって architectural な partition ではない。

## 4 つの Business AI Quadrants（業務 AI 四象限）

10 本の ADR と pair で adoption の診断フレームを成す:

|  | ワークフロー定義可 | 探索的 |
|---|---|---|
| **決定論で書ける** | (1) Script 象限 | (2) Algorithmic Search 象限 |
| **意味判断が必要** | (3) LLM Workflow 象限 | (4) Autonomous Agentic Loop 象限 |

現行 LLM 応用の大半は **LLM Workflow 象限** (決定論制御フロー + 役割を
明示した bounded LLM 呼び出し) で済むはずだが、業界の語彙にこの象限の
肯定形の名前がないため、**Autonomous Agentic Loop 象限** (実行時に LLM
自身が次の行動を決めるアーキテクチャ) に消去法で routing される。前者
を後者に被せると trilogy が診断したアカウンタビリティ崩壊の構造的源泉
になり、後者を pre-named gap-bearer（事前命名された責任引受者）なしで
運用すると ADR-0009 が防ぐべき configuration になる。詳細は
[`docs/quadrants/`](docs/quadrants/) の navigator (decision tree,
governance mapping, case studies, anti-patterns) を参照。

10 ADRs と 4 Quadrants は **二軸構造** を成す: ADRs は問いへの応答
(*何を制約すべきか、誰が責任を負うか*)、Quadrants は問いを切る軸
(どのアーキテクチャに業務を振り分ければ accountability distribution
が成立するか)。Phase (design / operation) は Quadrant と独立な第 3 の
dimension で、どの Quadrant も両 phase に出現しうる。ADR-0010 の
load-bearing rule は operation phase × Quadrant 4 の placement に
対してだけ Phase-crossing decision を要求する narrow なもの。

## narrative (記事系譜)

ADR の背後の argument は、6 部作 essay として展開された (trilogy +
architectural follow-up):

1. **[登れる壁に看板を立てても意味がない — AIエージェントに必要なのはガードレールではなくアカウンタビリティだ](https://github.com/shimo4228/zenn-content/blob/main/articles/ai-agent-accountability-wall.md)** (2026-04-06)
   *問題提起*: 登れる壁の看板は効かない。文明的パターンはプロンプト統治
   ではなく accountability architecture だ。

2. **[事故のあとで因果を辿れるか — エージェント設計が組織論に収斂するまで](https://github.com/shimo4228/zenn-content/blob/main/articles/agent-causal-traceability-org-adoption.md)** (2026-04-13)
   *応用*: 事故後の因果遡行は build time の構造を要求する。浮かび上がる
   構造は組織論の数世紀の蓄積と収斂する。

3. **[AIエージェントのブラックボックスは二層ある — 技術の限界とビジネスの都合](https://github.com/shimo4228/zenn-content/blob/main/articles/agent-blackbox-capitalism-timescale.md)** (2026-04-14)
   *障害分析*: weights への internalization (技術) と商業的秘匿
   (ビジネス) が共に透明性を阻害する。解は「全部見せる」でも「全部
   隠す」でもなく、因果遡行に必要な最小可視セットの定義。

4. **[ReAct エージェントが本当に必要な業務はどれか](https://github.com/shimo4228/zenn-content/blob/main/articles/react-agent-business-quadrant.md)** (2026-04-29)
   *architectural follow-up*: 業務 AI を 4 象限で腑分けすると、ReAct
   エージェントが正当に必要な領域は (4) 探索的タスクのみ。現行
   ecosystem が (3) 業務に (4) アーキテクチャを被せる category error
   は、accountability picture を破壊する根本原因の一つだと示す。

5. **[(3) LLM ワークフロー象限が語彙から脱落している — 続・ReAct エージェントの適用域](https://github.com/shimo4228/zenn-content/blob/main/articles/react-agent-business-quadrant-2.md)** (2026-04-30)
   *語彙の診断と責任主体の不在*: 業界の語彙に LLM ワークフロー象限が
   独立した呼び名として存在しないため、決定論で書けない業務すべてを
   ReAct ループに丸投げする消去法的選択が起きる。この人為的 redirect
   不能性に議論が集中する一方、ReAct 象限本来の attribution gap
   (runtime blend が生む寄与の事後分離不能性) と責任主体の不在 —
   自律性に伴う責任の引き受け先が現状エージェント側に存在しない
   こと — は議論の陰にある。

6. **[本番運用に ReAct は必要か — 設計フェーズと運用フェーズを分ける](https://github.com/shimo4228/zenn-content/blob/main/articles/react-agent-business-quadrant-3.md)** (2026-05-01)
   *時間軸の追加*: 業務は設計フェーズと運用フェーズに分かれる。設計
   フェーズは柔軟性を、運用フェーズは予測可能性を求め、両者の最適化
   軸は逆を向く。ReAct は設計フェーズの道具で、前 2 作が診断した
   (3) 業務に (4) アーキテクチャを被せる category error の根は、この
   2 つのフェーズの混同にある。ReAct の正当領域は設計フェーズの
   探索的タスク (コーディング、Deep Research、未知環境の探索) に
   絞り込まれ、本番運用に入った業務は (1) スクリプト象限と (3) LLM
   ワークフロー象限の組み合わせで動く。

全系譜は [`docs/inspiration.md`](docs/inspiration.md) を参照。

## 他プロジェクトとの関係

この repo は既存 2 プロジェクトの **sibling** (fork ではない):

- **[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)** —
  運用中の実装。ここの各 ADR はそちらの対応 ADR を、プロジェクト固有の
  詳細を剥がして再表現したもの。
- **[Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle)** —
  contemplative-agent 設計の *mechanism* 側を genre-neutral に保った
  sibling。AKC v2.0.0 (2026-04-19) が governance triplet を本 repo の
  ために抽出した。

層構造 — 3 つは相互フィードバックで共進化する:

```
  ─── theory layer ────────────────────────────────────────────

        AKC  ◄─────────────────────────────────►  AAP
        (mechanism — サイクル)                    (content — 実践)
        知識の流れ方                              attribution の分配

              ▲                                         ▲
              │                                         │
              ▼                                         ▼

  ─── implementation layer ────────────────────────────────────

                         contemplative-agent
                         (動いている system)
```

実装を動かすと摩擦が立ち上がる。摩擦が mechanism pattern (AKC) と
attribution judgment (AAP) を生み、磨かれた理論がまた実装に戻って
形を変える。

## 読む順序

1. [`docs/thesis.ja.md`](docs/thesis.ja.md) — *accountability distribution*、
   1 page の argument
2. [`docs/adr/README.ja.md`](docs/adr/README.ja.md) — ADR 索引
3. [`docs/adr/0001-security-by-absence.ja.md`](docs/adr/0001-security-by-absence.ja.md)
   — 最もクリーンな入口。末尾の audit test はそのまま動く
4. 6 記事 (上記 link) を発表順で
5. [`docs/quadrants/`](docs/quadrants/) — adoption navigator (decision
   tree, governance mapping, case studies, anti-patterns)
6. [`docs/manifesto.md`](docs/manifesto.md) — ADR が答えない文明 scale
   の問題

## この repo が主張しないこと

- 10 判断が完全である
- 抽出元の個別実装が永続する — *実装は溶ける、判断は残る*
- これらの判断が AI の方向性・労働再設計・社会 consent の大問題を
  解く。これらは open。[`docs/manifesto.md`](docs/manifesto.md) 参照
- top-down の AI governance policy が誤っている。policy は別の層で
  あり別の method。この repo は bottom-up 側 — 一人のオペレーターと
  一つのエージェントと、それを動かす摩擦から出てくるもの

## 出自

この抽出は 2026 年 4 月に Tatsuya Shimomoto
([@shimo4228](https://github.com/shimo4228)) によって最初にまとめ
られた。10 本の ADR と 4 つの Business AI Quadrants は、
[contemplative-agent](https://github.com/shimo4228/contemplative-agent)
の実装・運用と 2026 年 4–5 月公開の 6 部作 essay narrative spine を
通じて浮上した architectural decision および triage 判断を、
harness-neutral な形で再表現したもの。最初の 8 本はかつて
[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle)
の governance triplet として archive されていた素材を含み、ADR-0009 と
Quadrants navigator は 2026-04-29 / 04-30 の architectural follow-up
essay、ADR-0010 は 2026-05-01 の phase 区別 essay から派生した。

## 引用方法

これらの architectural decision records を利用・参照する場合は、
以下のように引用してほしい:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.19652014},
  url          = {https://doi.org/10.5281/zenodo.19652014},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (two experimental), paired with four Business AI Quadrants as the diagnostic frame and a Phase / Quadrant two-axis structure}
}
```

あるいは文中で:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.19652014

## License

MIT
