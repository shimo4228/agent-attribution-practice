言語: [English](README.md) | 日本語

# agent-attribution-practice

> **Agent Attribution Practice (AAP)** — 8 つの判断、固定された framework ではない。

自律 AI エージェントにおける **attribution** (誰が書いたか、誰が原因か、
誰が事後に辿れるか) の分配に関する 8 つの判断。
[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)
の実装・運用の摩擦から発見されたものであり、トップダウンの設計判断ではない。

## なぜこの repo が存在するか

現状の AI ガバナンスは **看板フェーズ** にいる。システムプロンプトに「X するな」
と書き、倫理ガイドラインを PDF で公開し、安全委員会を設置する。執行のない
テキスト。登れる壁に立てた看板。

歴史的に効いてきたのは看板ではなく **構造としてのアカウンタビリティ** だ。
実装されていない capability は呼び出せない。blast radius は設計で限定される。
行動を変える変更は必ず名前のある人間のサインオフを通る。事故後には因果を
遡行できる。人間社会はこのパターンを 3 世紀かけて磨いた。エージェントも
同じパターンを採るか、さもなくば説明責任の切れた層になる。

この repo の 8 本の ADR は「**何を制約すべきか、誰が責任を負うか**」という
形の判断を記録する。フレームワークから演繹したものではなく、実装から抽出
したものだ。

## 8 判断

| ADR | 原則 | Status |
|-----|------|--------|
| [0001](docs/adr/0001-security-by-absence.md) | Security by Absence — 危険な capability は制限するのではなく実装しない | accepted |
| [0002](docs/adr/0002-deterministic-prohibition-at-scaffolding.md) | Deterministic Prohibition at the Scaffolding Layer — 不在が取れない禁止は、モデル重みでなく scaffolding 側で決定論的に遮断する | accepted |
| [0003](docs/adr/0003-untrusted-content-boundary.md) | Untrusted Content Boundary — 蓄積メモリが権威を与えない | accepted |
| [0004](docs/adr/0004-single-external-adapter.md) | Single External Adapter — blast radius を構造で限定する | accepted |
| [0005](docs/adr/0005-human-approval-gate.md) | Human Approval Gate — 行動変更は名前のある人間の承認を要する | accepted |
| [0006](docs/adr/0006-causal-traceability.md) | Causal Traceability — あらゆるイベントが事後に再構成可能 | accepted |
| [0007](docs/adr/0007-scaffolding-visibility.md) | Scaffolding Visibility — 振る舞いは不透明な weight ではなくファイルに存在する | accepted |
| [0008](docs/adr/0008-one-agent-one-human.md) | One Agent, One Human — アカウンタビリティチェーンは名前のある個人で終端する | **experimental** |

最初の 3 本は **禁止強度の階層** を成す: absence > scaffolding enforcement >
untrusted boundary。次の 2 本 (0004, 0005) が topology と human-in-the-loop
を足す。中央の 2 本 (0006, 0007) はそれら制約が要求する artifact — 辿るべき
記録と、検査可能な scaffolding。8 本目は人間側の終端。

## 3 記事の narrative

ADR の背後の argument は、3 部作の essay として展開された:

1. **[登れる壁に看板を立てても意味がない — AIエージェントに必要なのはガードレールではなくアカウンタビリティだ](https://zenn.dev/shimo4228/articles/ai-agent-accountability-wall)** (2026-04-06)
   *問題提起*: 登れる壁の看板は効かない。文明的パターンはプロンプト統治ではなく
   accountability architecture だ。

2. **[事故のあとで因果を辿れるか — エージェント設計が組織論に収斂するまで](https://zenn.dev/shimo4228/articles/agent-causal-traceability-org-adoption)** (2026-04-13)
   *応用*: 事故後の因果遡行は build time の構造を要求する。浮かび上がる構造は
   組織論の数世紀の蓄積と収斂する。

3. **[AIエージェントのブラックボックスは二層ある — 技術の限界とビジネスの都合](https://zenn.dev/shimo4228/articles/agent-blackbox-capitalism-timescale)** (2026-04-14)
   *障害分析*: weights への internalization (技術) と商業的秘匿 (ビジネス) が
   ともに透明性を阻害する。解は「全部見せる」でも「全部隠す」でもなく、
   因果遡行に必要な最小可視セットの定義。

全系譜は [`docs/inspiration.md`](docs/inspiration.md) を参照。

## 他プロジェクトとの関係

この repo は既存 2 プロジェクトの **sibling** (fork ではない):

- **[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent)** —
  運用中の実装。ここの各 ADR はそちらの対応 ADR を、プロジェクト固有の
  詳細を剥がして再表現したもの。
- **[Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle)** —
  contemplative-agent 設計の *mechanism* 側を genre-neutral に保った sibling。
  AKC v2.0.0 (2026-04-19) が governance triplet を本 repo のために抽出した。

層構造:

```
                  contemplative-agent         (動いている system)
                          ↓
   ┌──────────────────────┴──────────────────────┐
   ↓                                             ↓
   AKC                                AAP
   (mechanism — サイクル)              (content — 実践)
   ↓                                             ↓
   知識の流れ方                         attribution の分配
```

## 読む順序

1. [`docs/thesis.md`](docs/thesis.md) — *accountability distribution*、1 page の
   argument
2. [`docs/adr/README.md`](docs/adr/README.md) — ADR 索引
3. [`docs/adr/0001-security-by-absence.md`](docs/adr/0001-security-by-absence.md) —
   最もクリーンな入口。末尾の audit test はそのまま動く
4. 3 記事 (上記 link) を発表順で
5. [`docs/manifesto.md`](docs/manifesto.md) — ADR が答えない文明 scale の問題

## この repo が主張しないこと

- 8 判断が完全である
- 抽出元の個別実装が永続する — *実装は溶ける、判断は残る*
- これらの判断が AI の方向性・労働再設計・社会 consent の大問題を解く。
  これらは open。[`docs/manifesto.md`](docs/manifesto.md) 参照
- top-down の AI governance policy が誤っている。policy は別の層であり
  別の method。この repo は bottom-up 側 — 一人のオペレーターと一つの
  エージェントと、それを動かす摩擦から出てくるもの

## 出自

この抽出は 2026 年 4 月に Tatsuya Shimomoto ([@shimo4228](https://github.com/shimo4228)) によって最初にまとめられた。8 本の ADR は、[contemplative-agent](https://github.com/shimo4228/contemplative-agent) の実装・運用を通じて浮上し、かつて [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) の governance triplet として archive されていた architectural decision を、harness-neutral な形で再表現したもの。

## 引用方法

これらの architectural decision records を利用・参照する場合は、以下のように引用してほしい:

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  url          = {https://github.com/shimo4228/agent-attribution-practice},
  note         = {Eight architectural decision records on accountability distribution in autonomous AI agents. DOI pending on Zenodo.}
}
```

あるいは文中で:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). [Zenodo DOI 発行待ち]

## License

MIT
