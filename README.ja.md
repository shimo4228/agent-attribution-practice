言語: [English](README.md) | 日本語

# agent-attribution-practice

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19652013.svg)](https://doi.org/10.5281/zenodo.19652013) [![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/agent-attribution-practice)

> **Agent Attribution Practice (AAP)**: 自律 AI エージェントで何を制約し、誰が責任を負うかについての 10 の判断と、その仕事にそもそもエージェントが要るかを決める 4 つの Business AI Quadrants（業務 AI 四象限）の組み合わせです。固定されたフレームワークではありません。

自律 AI エージェントにおける **attribution**（誰が振る舞いを書いたか、誰がその帰結を負うか、誰が事後に原因を辿れるか）の分配に関する 10 の判断と、その分配が成り立つアーキテクチャに業務を振り分ける 4 象限の診断フレームです。一人のオペレーターが [`contemplative-agent`](https://github.com/shimo4228/contemplative-agent) を実装・運用した摩擦と、7 本のエッセイから発見されたものであり、トップダウンで定めたものではありません。自律エージェントをある用途に入れてよいか、入れるならどう入れるかを判断するアーキテクト・オペレーター・ガバナンス読者に向けています。

ここにあるのはすべて読むための文書です。設計判断の記録（ADR、architecture decision record）、thesis、導入判断の navigator を Markdown で置いており、この repo にインストールするものはありません。導入判断の navigator、セキュリティの判断、観測性のパターンはインストールできる Agent Skill としても公開しています。それらは著者のほかの文章や repo と一緒に [著者のほかの仕事](#著者のほかの仕事) に並べています。

## なぜこの repo が存在するか

2026 年 4 月にシリーズの最初のエッセイ（[登れる壁に看板を立てても意味がない](https://zenn.dev/shimo4228/articles/ai-agent-accountability-wall)、[English](https://github.com/shimo4228/zenn-content/blob/main/articles-en/ai-agent-accountability-wall-en.md)）が論じたとおり、現状の AI ガバナンスは看板フェーズにいます。システムプロンプトに「X するな」と書き、倫理ガイドラインを PDF で公開し、安全委員会を設置します。執行のないテキストは、登れる壁に立てた看板です。歴史的に効いてきたのは看板ではなく、構造でアカウンタビリティを分配することです。実装されていない capability は呼び出せません。blast radius は設計で限定されます。行動を変える変更は必ず名前のある人間のサインオフを通ります。事故後には因果を遡行できます。この repo の thesis である *accountability distribution*（アカウンタビリティの分配）は、同じことがエージェントにも当てはまるという主張です。責任を一人に集めず、連鎖（レビュー、監査ログ、承認、事後検証）に分けて持たせます。ここにある 10 本の ADR は「**何を制約すべきか、誰が責任を負うか**」という形の判断を記録しています。

## 10 の判断

| ADR | 原則 | Status |
|-----|------|--------|
| [0001](docs/adr/0001-security-by-absence.ja.md) | Security by Absence — 危険な capability は制限するのではなく実装しない | accepted |
| [0002](docs/adr/0002-deterministic-prohibition-at-scaffolding.ja.md) | Deterministic Prohibition at the Scaffolding Layer — 不在が取れない禁止は、モデル重みでなく scaffolding（足場）側で決定論的に遮断する | accepted |
| [0003](docs/adr/0003-untrusted-content-boundary.ja.md) | Untrusted Content Boundary — 蓄積メモリが権威を与えない | accepted |
| [0004](docs/adr/0004-single-external-adapter.ja.md) | Single External Adapter per Agent Process — blast radius を構造で限定する | accepted |
| [0005](docs/adr/0005-human-approval-gate.ja.md) | Human Approval Gate — 行動変更は名前のある人間の承認を要する | accepted |
| [0006](docs/adr/0006-causal-traceability.ja.md) | Causal Traceability — あらゆるイベントが事後に再構成可能 | accepted |
| [0007](docs/adr/0007-scaffolding-visibility.ja.md) | Scaffolding Visibility — 振る舞いは不透明な重みではなくファイルに存在する | accepted |
| [0008](docs/adr/0008-one-agent-one-human.ja.md) | One Agent, One Human — アカウンタビリティチェーンは名前のある個人で終端する | **experimental** |
| [0009](docs/adr/0009-triage-before-autonomy.ja.md) | Triage Before Autonomy — 自律ループのアーキテクチャを採用すると、除去できない **attribution gap**（モデル・ツール・文脈の寄与を事後に分けられない状態）を引き受けることになる | **experimental** |
| [0010](docs/adr/0010-phase-separation.ja.md) | Phase Separation — 自律ループを operation phase（設計ではなく本番運用）に置くなら、その境界を越える判断を記録する | **experimental** |

最初の 3 本は **禁止強度の階層** を成します（absence > scaffolding enforcement > untrusted boundary）。0004 と 0005 が構造的トポロジと human-in-the-loop を加え、0006 と 0007 はそれらの制約が要求する成果物（辿るべき記録と、検査可能な scaffolding）です。0008 は人間側の終端です。ADR 0009 と 0010 は二段の振り分けです。0009 はその仕事にそもそも自律ループが要るかを問い、0010 はどのフェーズ（設計か運用か）で動かしてよいかを問います。

ADR-0001 は 1 つのコマンドで一次点検できます。このパターンが捉えるのは Python 風のプロセス起動・eval・HTTP 呼び出しです。`model.eval()` のような無害な名前にもヒットし、他の言語で書かれた呼び出しは捉えないので、除外対象だけでなくパターンも自分の言語やスタックに合わせて調整してください。クリーンなコードベースなら残るヒットは 0 件か、ADR が明示的に許した adapter の中だけです。

```bash
git grep -nE "subprocess|os\.system|\beval\(|\bexec\(|urlopen|requests\.(get|post|put|delete|patch)" \
  -- ':!docs' ':!examples' ':!tests'
```

## Business AI Quadrants

Business AI Quadrants（業務 AI 四象限）は、仕事を決定論で書けるか意味判断が要るかと、ワークフローを事前に定義できるか探索的かで 4 つに分けます。

|  | ワークフロー定義可 | 探索的 |
|---|---|---|
| **決定論で書ける** | (1) Script 象限 | (2) Algorithmic Search 象限 |
| **意味判断が必要** | (3) LLM Workflow 象限 | (4) Autonomous Agentic Loop 象限 |

象限を導入したエッセイ（[ReAct エージェントが本当に必要な業務はどれか](https://zenn.dev/shimo4228/articles/react-agent-business-quadrant)、2026-04-29）が論じるとおり、現行の LLM 応用の大半は **LLM Workflow 象限**（決定論の制御フロー + 役割を明示し範囲を限った LLM 呼び出し）に属し、**Autonomous Agentic Loop 象限**（次の一手を実行時に LLM が決める）ではありません。前者を後者に振り分けてしまうことが、著者のエッセイ（[エッセイと論文](#エッセイと論文)）が診断したアカウンタビリティ崩壊の多くを生む構造的な源泉です。自律ループは、ADR-0009 の行で述べた attribution gap を抱えます。そのギャップを引き受ける者として稼働前に名指される相手を **gap-bearer** と呼び、gap-bearer なしで自律ループを運用することは、ADR-0009 が防ぐ失敗の型です。ADR は問いへの応答（*何を制約すべきか、誰が責任を負うか*）で、Quadrants は業務をその応答が成立する場所へ振り分けます。両者は二つの軸を成し、Phase（設計中に動かすか、本番の運用で動かすか）は独立な第 3 の次元です。

## エージェント導入判断の navigator として使う

この repo は読むだけでなく、順に辿って使うことを意図しています。ある用途に自律エージェントを入れてよいか判断したいとき、あるいは既に入れたものを監査したいときは、clone して coding agent を `AGENTS.md` に向け、壁打ち相手として使ってください。

1. [`docs/quadrants/decision-tree.ja.md`](docs/quadrants/decision-tree.ja.md) — 5 問の triage で業務を 4 象限のどれかに振り分ける
2. [`docs/quadrants/governance-mapping.ja.md`](docs/quadrants/governance-mapping.ja.md) — その象限のガバナンス要件
3. 関連 ADR。特に振り分けの 2 本、0009（自律ループが要るか）と 0010（design phase か operation phase か）
4. [`docs/quadrants/anti-patterns.ja.md`](docs/quadrants/anti-patterns.ja.md) — 既知の失敗モードとの最終照合

*例（説明用）:* 返金承認を自律で回すループは step 1 で Autonomous Agentic Loop 象限に振り分けられ、step 3 で 10 本の ADR すべてが適用対象になります。稼働前に gap-bearer を名指すこと（ADR-0009）もその一つです。ほかの振り分け（coding agent、FAQ 分類、請求書照合、契約レビューなど）は [`docs/quadrants/case-studies.ja.md`](docs/quadrants/case-studies.ja.md) にあります。いずれも日付つきの説明用の振り分けで、推奨する参照アーキテクチャではありません。

ADR は判断の出発点であって正解ではありません。自分の文脈に応じて読み替えてください。

## エッセイと論文

議論は、2026 年 4〜5 月に公開した 7 本のエッセイで展開しました。三部作（問題提起 → 事故後の因果遡行 → ブラックボックス二層分析）と、設計面の続編 4 本（4 象限による振り分け → 語彙の診断 → フェーズの区別 → skill 設計のグラデーション）です。各記事のリンク・日付・要約と、ADR ごとの系譜は [`docs/inspiration.md`](docs/inspiration.md)（英語）にあります。

二つの companion position paper（姉妹編の立場表明論文）が、エッセイを特定の harness に依存しない主張に蒸留しています（オープンアクセス、CC BY 4.0。concept DOI は常に最新版へつながる代表 DOI です）。

- Shimomoto, T. (2026). *Distributing Accountability, Not Capability: Phase Separation and the LLM Workflow Quadrant in Autonomous AI Agent Architectures*（essay 4–7）. Zenodo. [doi:10.5281/zenodo.20353789](https://doi.org/10.5281/zenodo.20353789) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6817598)
- Shimomoto, T. (2026). *The Two-Layer Black Box: Operator Visibility, Commercial Secrecy, and a Minimum Disclosure Set for Accountable Autonomous AI Agents*（essay 1–3）. Zenodo. [doi:10.5281/zenodo.20355907](https://doi.org/10.5281/zenodo.20355907) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6823878)

## 外部との対応

時間とともに古くなる二つの対応づけを、ADR が vendor とフレームワークに中立であり続けるよう ADR の外に置いています。[`docs/industry-mapping.ja.md`](docs/industry-mapping.ja.md) は、2026 年の業界のリリース（ポリシーゲート、エージェントの身元を表す基本機能、sponsor 制度、vendor をまたぐ監査）を、それらが残している判断と対応づけます。[`docs/policy-mapping/`](docs/policy-mapping/README.ja.md) は、ADR と Quadrants を NIST AI RMF 1.0（Generative AI Profile を含む）、ISO/IEC 42001:2023、EU AI Act、Singapore Model AI Governance Framework for Agentic AI に対応づけています。これは一つの読みであり引用のための資料であって、準拠の証明ではありません。

## 読む順序

1. [`docs/thesis.ja.md`](docs/thesis.ja.md) — アカウンタビリティの分配（accountability distribution）についての論証
2. [`docs/glossary.ja.md`](docs/glossary.ja.md) — 用語定義 (accountability distribution、attribution gap、4 象限、Phase)
3. [`docs/adr/README.ja.md`](docs/adr/README.ja.md) — ADR 索引
4. [`docs/adr/0001-security-by-absence.ja.md`](docs/adr/0001-security-by-absence.ja.md) — 最もクリーンな入口。末尾の audit test が上に示したコマンドです
5. 7 本のエッセイを発表順で（[`docs/inspiration.md`](docs/inspiration.md)（英語）に一覧）
6. [`docs/quadrants/`](docs/quadrants/README.ja.md) — 導入判断の navigator（決定木、ガバナンス要件の対応、事例、アンチパターン）
7. [`docs/manifesto.md`](docs/manifesto.md)（英語） — ADR が答えない文明規模の問題
8. [`docs/social-consequence.ja.md`](docs/social-consequence.ja.md) — 7 本の外にある姉妹編のエッセイ。アカウンタビリティが分配されないとき社会で何が起きるかを扱い、ADR からは分けて置いています

## この repo が主張しないこと

- 10 判断が完全である
- 抽出元の個別実装が永続する — *実装は溶ける、判断は残る*
- これらの判断が AI の方向性・労働再設計・社会的合意の大問題を解く。これらは未解決。[`docs/manifesto.md`](docs/manifesto.md)（英語）参照
- トップダウンの AI ガバナンス政策が誤っている。政策は別の層であり別の方法。この repo はボトムアップ側 — 一人のオペレーターと一つのエージェントと、それを動かす摩擦から出てくるもの

## 出自

2026 年 4 月に Tatsuya Shimomoto（[@shimo4228](https://github.com/shimo4228)、[ORCID 0009-0002-6168-4162](https://orcid.org/0009-0002-6168-4162)）が最初にまとめました。AAP の兄弟プロジェクトである [Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) は、同じ実装の摩擦を仕組みのパターン（知識の流れ方）に蒸留し、AAP は attribution の分配を記録します。両者は動いているシステムを通じて循環します。[`contemplative-agent`](https://github.com/shimo4228/contemplative-agent) の運用が両者の蒸留する摩擦を生み、磨かれた判断がその実装を作り変えます。

## 引用方法

```bibtex
@software{shimomoto2026aap,
  author       = {Shimomoto, Tatsuya},
  title        = {Agent Attribution Practice (AAP)},
  year         = {2026},
  doi          = {10.5281/zenodo.21218784},
  url          = {https://doi.org/10.5281/zenodo.21218784},
  note         = {Ten architectural decision records on accountability distribution in autonomous AI agents (three experimental), paired with four Business AI Quadrants as the diagnostic frame in a two-axis structure, with Phase as an independent third dimension}
}
```

あるいは文中で:

> Shimomoto, T. (2026). Agent Attribution Practice (AAP). doi:10.5281/zenodo.21218784

冒頭の badge は **concept DOI**（[10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)、常に最新版へつながります）を、上の BibTeX は現行リリースの **version DOI** を指します。

## 著者のほかの仕事

- **[ReAct エージェントが本当に必要な業務はどれか](https://zenn.dev/shimo4228/articles/react-agent-business-quadrant)**（[English](https://dev.to/shimo4228/where-react-agents-are-actually-needed-in-business-33do)）: 上で要約した、現行の LLM 応用の大半が自律ループではなく LLM Workflow 象限に属する理由の論証全体です。
- **[AIエージェントの「なぜその判断？」に答えるオブザーバビリティ設計3パターン](https://zenn.dev/shimo4228/articles/agent-observability-patterns)**（[English](https://dev.to/shimo4228/why-did-my-agent-decide-that-3-observability-patterns-ami)）: ADR-0006 と ADR-0005 の背後にある観測性パターンを、それが生まれた稼働中のエージェントで見せています。
- **[agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage)**（英語）: AI の仕事を、何かを作る前に script・algorithmic search・LLM workflow・自律エージェントのどれかへ振り分ける 5 つの問いです。象限 navigator と ADR-0009 / ADR-0010 をインストールできる形にしたものです。
- **[llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles)**（英語）: 自律エージェントの構造的なセキュリティ（capability を見張るのでなく取り除く、蓄積した内容を信頼しない、1 プロセスに外部 adapter は 1 つ）です。ADR-0001〜ADR-0004 をインストールできる形にしたものです。
- **[agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns)**（英語）: 介入する前にエージェントを観測するための 3 つの skill（replayable audit logs、read-only instruments、shadow-mode validation）です。ADR-0006 と ADR-0005 をインストールできる形にしたものです。
- **[shimo4228](https://github.com/shimo4228/shimo4228)**: 著者の hub です。AAP をほかの長期プロジェクトと並べ、それぞれの DOI を載せています。

## License

MIT

<details>
<summary>ツールと AI アシスタント向けの資料</summary>

Agent Attribution Practice（AAP）は、自律 AI エージェントをある用途に入れてよいか、入れるならどう入れるかを判断するアーキテクト・オペレーター・ガバナンス読者に向けて、attribution（エージェントの振る舞いを誰が書いたか、誰がその帰結を負うか、誰が事後に原因を辿れるか）をどう分配すべきかを、10 本の設計判断の記録（ADR）と 4 象限の診断フレームで記録する研究リポジトリです。

この repo があるのは、現状の AI ガバナンスの多くが執行のないテキスト（システムプロンプトの指示、ガイドラインの PDF、安全委員会）にとどまる一方で、歴史的に効いてきたのは構造によるアカウンタビリティの分配だからです。存在しない capability は呼び出せず、blast radius は設計で限られ、行動の変更には名前のある人間のサインオフが要り、事故は後から再構成できます。判断は著者の contemplative-agent の実装・運用と 7 本のエッセイを通じて浮上したもので、ADR が特定の harness・vendor 製品・ガバナンスのフレームワークを名指さない形に書き直しています。vendor やフレームワークとの対応は、時期に依存する別の対応文書に置いています。

基本の事実: MIT ライセンス。実行するコードはありません（Markdown の文書と、graph.jsonld・llms.txt・llms-full.txt・CITATION.cff・codemeta.json などの機械可読なメタデータ）。英語が正本で、README・ADR・thesis・glossary・象限 navigator・業界との対応ファイル・政策との対応ディレクトリ・社会的帰結のエッセイには日本語版（`*.ja.md`）があります。2026 年 4 月から Tatsuya Shimomoto（@shimo4228、ORCID 0009-0002-6168-4162）がまとめています。状態: 稼働中で、著者が手作業で管理しています。ADR-0001〜ADR-0007 は accepted、ADR-0008〜ADR-0010 は experimental です。必要なもの: 読むだけなら何も要りません。navigator として使うなら clone して coding agent を `AGENTS.md` に向けます。concept DOI [10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013) は常に最新版へつながります。引用には「引用方法」の BibTeX か `CITATION.cff` を使ってください。

核になる考え。*Accountability distribution*: 責任を一人に集めず、連鎖（レビュー、監査ログ、承認、事後検証）に分けて持たせることです。この repo の主張は、これがエージェントにも当てはまるというものです。*禁止強度の階層*: absence（ADR-0001）> scaffolding enforcement（ADR-0002）> untrusted boundary（ADR-0003）。禁止は、いまの層で capability を抑えきれないときだけ次の層へ下ります。*Business AI Quadrants*: 決定論で書ける仕事か意味判断が要る仕事かと、ワークフローを事前に定義できるか探索的かを掛け合わせた 4 象限で、Script・Algorithmic Search・LLM Workflow・Autonomous Agentic Loop の各象限になります。*Attribution gap*: モデルの出力、ツールの選択、履歴、プロンプトの文脈を実行時に混ぜるアーキテクチャでは、寄与を事後に分けられなくなります。このギャップは Autonomous Agentic Loop 象限に固有のものです。*Gap-bearer*: そのギャップを引き受ける者として、稼働前に名指される相手です（ADR-0009）。*Phase*: design と operation の区別で、Quadrant とは独立な次元です。自律ループを operation phase に置くなら、Phase-crossing decision の記録が要ります（ADR-0010）。

例（説明用）: 返金承認を自律で回すループは、decision tree の最初の段階で Autonomous Agentic Loop 象限に振り分けられます。すると 10 本の ADR すべてが適用対象になり、ループを稼働させる前に gap-bearer を名指すという ADR-0009 の要件もそこに含まれます（ほかの振り分けは `docs/quadrants/case-studies.ja.md` にあり、これも説明用です）。

リンク: [graph.jsonld](graph.jsonld) は機械可読の関係マップ（Quadrants、ADR、禁止の層、Phase）です。[llms.txt](llms.txt)（英語）は簡潔な索引、[llms-full.txt](llms-full.txt)（英語）は統合された参照です。[docs/adr/](docs/adr/README.ja.md)、[docs/quadrants/](docs/quadrants/README.ja.md)、[docs/glossary.ja.md](docs/glossary.ja.md) に記録・navigator・定義があります。インストールできる skill は [agent-adoption-triage](https://github.com/shimo4228/agent-adoption-triage)（ADR-0009 と ADR-0010）、[llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles)（ADR-0001〜ADR-0004）、[agent-observability-patterns](https://github.com/shimo4228/agent-observability-patterns)（ADR-0006 と ADR-0005）です。著者のエコシステム全体は hub の [graph.jsonld](https://github.com/shimo4228/shimo4228/blob/main/graph.jsonld) にあります。

</details>
