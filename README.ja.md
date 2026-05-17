言語: [English](README.md) | 日本語

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652013.svg)](https://doi.org/10.5281/zenodo.19652013)

> **Agent Attribution Practice (AAP)** — 10 の判断と 4 つの Business AI
> Quadrants（業務 AI 四象限）、固定された framework ではない。

<details>
<summary>AI 向け推奨読み順</summary>

1. [`graph.jsonld`](graph.jsonld) — 機械可読な関係マップ正本（Quadrants、ADRs、禁止強度階層、Phase）
2. [`llms.txt`](llms.txt) — コンパクトなナビゲーション索引
3. [`llms-full.txt`](llms-full.txt) — 統合された事実参照
4. README およびリポジトリ固有 docs — narrative と詳細

shimo4228 全体の研究エコシステムの関係マップは以下を参照:
https://github.com/shimo4228/shimo4228/blob/main/graph.jsonld

</details>

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

ADR の背後の argument は、2026 年 4-5 月に zenn で公開された 7 部作
essay として展開された。最初の 3 つは trilogy (問題提起 → 事故後の
因果遡行 → ブラックボックス二層分析)、後の 4 つは architectural
follow-up (4 象限 triage → 語彙の診断と principled attribution gap →
設計 / 運用 phase の区別 → skill-design gradient への phase descent)。

各記事の URL とタイトル、1 段落のサマリは
[`docs/inspiration.md`](docs/inspiration.md) を正本として参照。

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

## 業界の mechanism layer との関係

2026 Q2 に複数の industry release が AAP principle の *mechanism
layer* を populate した ── sub-millisecond ポリシーゲート、agent
identity primitive、sponsor 制度、cross-vendor 監査、cross-cloud
registry sync。

Mechanism layer が ship していないのは judgment layer である。
Sponsor は assign できるが、**誰が** sponsor になるべきか・それが
何の commitment を意味するかは product に含まれない。Cross-cloud
registry は sync できるが、Single External Adapter の設計判断
(設計時に blast radius を bound する、事後 observation ではない)
は product に含まれない。Policy engine は agent action を
intercept できるが、prohibition-strength hierarchy (absence >
scaffolding > untrusted boundary) でどの prohibition がどの層に
属するか決める判断は product に含まれない。AAP がこの judgment
layer を埋める。両 layer は補完関係で、mechanism layer の普及は
judgment layer の必要性を **減らすのではなく増す**。

各 artifact がどの ADR の mechanism instance に対応するか、何を
ship して何を ship していないかの per-artifact 対応は
[`docs/industry-mapping.md`](docs/industry-mapping.md)
(time-bound、製品の進化と共に decay する別ファイル) で保持される。
ADR 自体は touch せずに済む。

## AI ガバナンス framework との関係

AAP の ADR と Quadrant は、各国 / 国際の AI ガバナンス framework
にも読み合わせる。Phase 1 では **NIST AI Risk Management
Framework 1.0** (NIST.AI.100-1、2023 年 1 月) と **Generative AI
Profile** (NIST.AI.600-1、2024 年 7 月)、**ISO/IEC 42001:2023**
AI Management System (first edition、2023 年 12 月) を扱う。EU AI
Act と OECD AI Principles は後続 phase に延期 ── decay cadence と
条項粒度が十分異なるため、4 つを 1 release にまとめると noise が
混ざる。

framework が ship するのは *構造* ── NIST は AI risk management
を 4 つの function (GOVERN、MAP、MEASURE、MANAGE) を中心に組織化
し、GAI Profile が generative-AI 固有のリスクカテゴリを追加する;
ISO 42001 は Annex SL Harmonized Structure (ISO 27001、ISO 9001、
ISO 14001 と共有) と PDCA cycle 上に構築された AI Management System
を規定し、Annex A controls が policy、role、lifecycle、data、
transparency、third-party relationship を扱う。framework が ship
していないのは、それら構造を autonomous-agent subset 向けに populate
する *judgment layer* である: NIST function を restriction ではなく
absence で answer すべきはいつか、AIMS のどの clause が non-LLM gate
を要求するか、ADR-0009 で named された gap-bearer は ISO 42001 の
accountability chain のどこに位置するか、Phase-crossing decision が
AIMS の PDCA cycle 内で ADR-0009 の上に何を加えるか。AAP がその
judgment layer を記録する; 両 layer は補完関係。

各 AAP ADR の specific NIST function / GAI リスクカテゴリ / ISO
42001 clause および Annex A area への対応 (および framework 側から
入る reader のための reverse index) は
[`docs/policy-mapping/`](docs/policy-mapping/README.ja.md) で保持
される ── time-bound directory、製品 release cycle ではなく
framework 改訂 cycle (年単位〜数年単位) で decay する。ADR 自体は
framework-neutral に保たれる。

これは compliance attestation ではない。NIST AI RMF の authoritative
interpretation は NIST、ISO/IEC 42001 は ISO/IEC と accredited
certification body、framework の特定 jurisdiction への適用は
qualified legal / compliance counsel に残る。

## 読む順序

1. [`docs/thesis.ja.md`](docs/thesis.ja.md) — *accountability distribution*、
   1 page の argument
2. [`docs/adr/README.ja.md`](docs/adr/README.ja.md) — ADR 索引
3. [`docs/adr/0001-security-by-absence.ja.md`](docs/adr/0001-security-by-absence.ja.md)
   — 最もクリーンな入口。末尾の audit test はそのまま動く
4. 7 記事 (上記 link) を発表順で
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
の実装・運用と 2026 年 4–5 月公開の 7 部作 essay narrative spine を
通じて浮上した architectural decision および triage 判断を、
harness-neutral な形で再表現したもの。最初の 8 本はかつて
[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle)
の governance triplet として archive されていた素材を含み、ADR-0009 と
Quadrants navigator は 2026-04-29 / 04-30 の architectural follow-up
essay、ADR-0010 は 2026-05-01 / 05-02 の phase 区別 essay と
skill-design gradient essay から派生した。

## 引用方法

これらの architectural decision records を利用・参照する場合は、
以下のように引用してほしい:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.19920228},
  url          = {https://doi.org/10.5281/zenodo.19920228},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (two experimental), paired with four Business AI Quadrants as the diagnostic frame and a Phase / Quadrant two-axis structure}
}
```

あるいは文中で:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.19920228

## License

MIT
