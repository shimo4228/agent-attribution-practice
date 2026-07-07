# 業界 mechanism layer マッピング (Industry mechanism layer mapping)

**Status:** Informational, time-bound (2026 Q2 snapshot)
**Date:** 2026-05-02
**Maintenance posture:** This file decays. The ADRs do not.

## Purpose

本 repository の ADR は autonomous AI agent システムにおける
attribution の分配に関する判断を記録する。2026 Q2 にかけて、複数の
業界 release がそれら判断が記述する *mechanism layer* を populate
した。本ファイルは執筆時点で、特定 vendor mechanism と、それが
instance する ADR との対応を記録する。

ADR 本文に埋め込まずに別ファイルに切り出した理由は、本 repository
の core thesis を遵守するため: *implementation dissolves; judgment
persists* (実装は溶ける、判断は残る)。Vendor 製品は rename されたり、
discontinue されたり、restructure される。ADR はそうならない。製品の
進化に応じて本ファイルだけ更新すれば、ADR には触れずに済む。

ADR を産み出した narrative spine は
[`inspiration.ja.md`](inspiration.md) を参照 (英語のみ)。
AAP の business AI vendor mechanism layer に対する高レベルの
positioning は
[`../README.ja.md`](../README.ja.md#業界の-mechanism-layer-との関係)
を参照。用語の区別 (sponsor vs gap-bearer、agent governance vs
accountability distribution、observability vs structural
enforcement) は [`glossary.ja.md`](glossary.ja.md) を参照。

## ADR ごとの対応

### ADR-0002 — Scaffolding 層での決定論的禁止

[Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)
(2026-04-02、MIT-licensed open source) は *Agent OS* を ship する
─ 全 agent action を intercept する sub-millisecond stateless ポリ
シーエンジンで、OWASP Agentic Top 10 への対応を謳う。
[ADR-0002](adr/0002-deterministic-prohibition-at-scaffolding.ja.md)
の原理の mechanism instance として、model-weight 遵守ではなく
scaffolding 層で発火する決定論的 gate を提供する。提供していないのは、
どの prohibition が absence 層 (ADR-0001) / scaffolding 層 (本 ADR) /
untrusted-content 層 (ADR-0003) に属するかを決める
[prohibition-strength hierarchy](glossary.ja.md#prohibition-strength-hierarchy禁止強度の階層)
の判断; 製品資料は全 prohibition を scaffolding 層の concern として
扱う。両層は補完関係: toolkit が原理を技術的に enforce 可能にし、
ADR が原理を *いつ・なぜ・どのコストで* enforce すべきかを記録する。

### ADR-0006 — Causal traceability

[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01、$15/user/month standalone もしくは Microsoft 365 E7
─ Frontier Suite とも呼ばれる ─ の一部) の一部として ship された
[Microsoft Purview Data Security Investigations](https://learn.microsoft.com/en-us/microsoft-agent-365/overview)
は、Microsoft 365 / Azure / 統合済みサードパーティ AI service の
ログを相関させ AI interaction の data lifecycle を再構成する;
AI-sensitive な情報タイプの自動分類、保持ラベル、DLP ポリシー適用を
備える。
[ADR-0006](adr/0006-causal-traceability.ja.md) の causal traceability
要件の mechanism instance として、「あらゆる event が事後に再構成
可能」性質に最も近い商業製品。提供していないのは、なぜ causal
traceability が runtime observability practice ではなく build-time
structural requirement でなければならないかの argument (ADR の core
point)、および、どの approval がどの scaffolding バージョンを gate
したか (再構成が named human で終端するために必要な ADR-0005 と紐
づく audit trail) の記録。両層は補完関係: Purview が再構成を技術的に
可能にし、ADR が **どこで終端しなければならないか** を記録する。

### ADR-0007 — Scaffolding visibility

[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01) と共に named された Microsoft の *Shadow AI* framing
は、[ADR-0007](adr/0007-scaffolding-visibility.ja.md) が記録する不在
問題 ── scaffolding が visible でないと governance が適用できない ──
を業界レベルで認識した証拠。Microsoft Defender と Intune は Windows
デバイス上の local agent (OpenClaw、GitHub Copilot CLI、Claude Code)
を既に検出しており、2026-06 のリリースで資産コンテキストマッピング
(各 agent がどのデバイスで動作し、どの MCP server に接続し、どの
クラウドリソースに触るか) の追加が予定されている。Mechanism instance
としては検出側の visibility ── 以前は invisible だった scaffolding
を operator から発見可能にする。提供していないのは、scaffolding
visibility が weights internalization の構造的代替であるという
argument、*healthy dissolution* (scaffolding が clear な会話 pattern /
読みやすいファイルに溶け込む) と *unhealthy internalization*
(scaffolding が weights に吸収される) の境界、ADR が引く
operator-vs-public visibility の区別。両層は補完関係: Microsoft が
Shadow AI gap を運用的に表面化させ、ADR が **なぜその gap が重要か、
どこに visibility line を引くべきか** を記録する。

### ADR-0008 — One agent, one human

[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01) の identity-management 軸として ship された
[Microsoft Entra Agent ID](https://learn.cloudpartner.fi/posts/microsoft-entra-agent-id-agentic-identity-ai-agents)
は *sponsor 制度* を導入する: 各 agent に責任者を割り当て (user /
device に並ぶ agent 専用 identity カテゴリ)、孤立した認証情報の蓄積
を防ぐ。これは [ADR-0008](adr/0008-one-agent-one-human.ja.md) の
*agent ごとに named human* という要件の mechanism instance。提供して
いないのは、sponsor を
[pre-named gap-bearer](glossary.ja.md#pre-named-gap-bearer事前命名された責任引受者)
に変える structural commitment (attribution-gap 吸収の acknowledge、
legal subject standing、contractual authority)、accountable operator
が team / role / committee ではなく個人でなければならないという
normative argument、ADR-0009 の design-time triage との接続
(gap-bearer は deployment commitment であり runtime assignment では
ない)。両層は補完関係: Entra Agent ID が agent ごとの ownership を
administrative に可能にし、ADR は administrative assignment の
**先に何が要求されるか** を記録する。
[Sponsor (vendor terminology)](glossary.ja.md#sponsor-vendor-terminologysponsor--業界語彙)
glossary エントリが区別を記録する。

## その他の 2026 Q2 industry artifact (単一 ADR 対応なし)

以下は隣接層 (LLM Workflow Quadrant のツール、別クラウドの surface、
cross-environment observability) を populate するが、ADR との
1 対 1 対応は持たない:

- **[Microsoft Agent Framework 1.0](https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-version-1-0/)**
  (2026-04-03)。.NET / Python SDK で Semantic Kernel と AutoGen を
  統合、full MCP protocol support、1.0 で A2A support を announce
  (full A2A 1.0 support は launch post で「forthcoming」と明記)、
  graph-based workflow engine と middleware hooks。Anthropic の
  「Building Effective Agents」pattern と並んで LLM Workflow
  Quadrant のツーリングを populate する。単一 ADR の instance では
  なく、quadrant 分解の *workflow* 寄りに位置する。
- **AWS Bedrock + OpenAI 統合** (2026-04-28 に limited preview として
  発表; GA は launch 時に imminent と説明された)。Microsoft stack
  とは独立した agent-management surface。同じ judgment-layer 上の
  問い (誰が gap-bearer か、autonomous loop を operation phase に
  置く時の Phase-crossing decision は何か) が適用される。
- **Google Vertex AI Agent Builder** (Google Cloud Next 2026 で
  Gemini Enterprise Agent Platform へ rebrand)。独立した
  agent-platform line。Microsoft の cross-cloud registry preview に
  sync target として含まれており、entro.security が指摘する
  fragmentation 問題 ─ 各クラウド provider が独自の identity model
  を持ち、registry sync が surface しても解消はできない複数の
  governance blind spot ─ を実証する。
- **[entro.security](https://entro.security/blog/microsoft-agent-365-pushes-ai-identity-forward-but-enterprise-agents-still-need-cross-environment-governance/)
  / oasis.security**。単一 vendor 製品が到達できない
  cross-environment governance gap (GitHub Actions、Slack、Jira、
  Jenkins、その他 non-Microsoft SaaS) を target する identity-security
  スタートアップ。core observation ─ *各クラウド provider が独自の
  agent identity model を構築し、fragmented control と複数の
  governance blind spot を生む* ─ は ADR-0004 Single External
  Adapter 原理と productive tension の関係: 彼らの mechanism は既に
  複数の external surface を獲得した agent への post-hoc
  observability、ADR-0004 は surface count を design 時点で bound
  する upstream の design judgment。両層に価値がある;
  [Observability vs structural enforcement](glossary.ja.md#observability-vs-structural-enforcementobservability-vs-構造的強制)
  glossary エントリが区別を記録する。

## accountability の市場としての AI 賠償責任保険 (2026 上半期)

2026 年上半期、保険業界は AI リスクを補償のグレーゾーンから外へ動かし、
独立した商品として引き受け始めた。ADR が設計層の判断として記録してきた
ものの、市場側での具体化である:

- **[ISO/Verisk の生成 AI CGL 除外裏書き](https://core.verisk.com/Insights/Emerging-Issues/Articles/2025/July/Week-4/Emerging-Risks-in-ISO-General-Liability-Multistate-Filing)**
  (2026-01-01 発効)。Verisk の ISO はオプションの裏書き様式 —
  CG 40 47 (Coverage A + B)、CG 40 48 (Coverage B のみ)、CG 35 08
  (生産物・完成作業) — を導入し、企業総合賠償責任保険 (CGL) から
  生成 AI 起因の賠償責任を明示的に除外できるようにした
  ([Gallagher が実ポリシーでの出現を確認](https://www.ajg.com/news-and-insights/iso-introduces-generative-ai-exclusion-in-commercial-general-liability-policies/))。
  除外は保険会社ごとの任意採用であり業界一律ではないが、効果として
  AI リスクを専用商品へ押し出す。
- **[Munich Re aiSure](https://www.munichre.com/en/solutions/for-industry-clients/insure-ai.html)**。
  性能保証 (performance warranty) を裏付けとする AI 保険。
  文書化された限度額は標準的なモデル保険で約 $5M、
  [Mosaic 提携](https://www.mosaicinsurance.com/resources/press-releases/~/mosaic-partners-with-munich-res-aisure-to-provide-pioneering-coverage-for-ai-vendors/)
  経由の初期キャパシティで最大 $15M。別プロバイダの AIUC は
  AI エージェント損失を最大 $50M まで引き受ける。
- **[Klaimee](https://www.ycombinator.com/launches/QCC-klaimee-insures-your-ai-agents)**
  (Y Combinator)。リスク評価 → 認証レポート → 金融保証 → AI 賠償
  責任保険の 4 ステップ。100 本超の行動プローブ (プロンプト
  インジェクション・ジェイルブレイク・決定ドリフト・データ漏洩・
  バイアス出力) で 8 次元リスクスコア (スコープ、データ抜き取り、
  無許可アクション、出力整合性、敵対的操作、行動安定性、モデル
  ドリフト、オペレーショナルコントロール) を算出し A–F の
  レターグレードに変換、グレードは引受判断に入る。
- **[Mount](https://www.ycombinator.com/launches/QUI-mount-the-ai-agent-insurance-carrier)**
  (Y Combinator, Spring 2026)。「The AI Agent Insurance Carrier」を
  自称し、スキャン / レッドチーム → 残余リスク定量化 → 付保、と
  位置づける。執筆時点で fronting carrier や認可キャリアとしての
  地位は公開されておらず、「carrier」は自己ポジショニングであって
  検証済みの規制上の地位ではない。

規制面の隣接: Colorado AI Act
([SB 24-205](https://leg.colorado.gov/bills/sb24-205)) は、文書化
されたリスク管理プログラム (AI リスク管理フレームワークや AI
マネジメントシステム標準への準拠等) が存在する場合に deployer へ
「合理的注意」の反証可能な推定を与える — 立証可能な監督が、法的
にも引受上も有利な扱いの条件になる。なお「合理的監督基準
(reasonable oversight standard)」という名前の法基準を制定した
法域は存在しない。2026 年の言説に流通するその種の主張は、
Colorado の推定・EU 法の人間監視義務・撤回済みの EU 責任指令案を
混合した合成である。

AAP の読み: エージェントの損失の金融的受け皿として保険会社が
契約上事前に指名されることは、*pre-named gap-bearer* の判断
([ADR-0008](adr/0008-one-agent-one-human.ja.md) /
[ADR-0009](adr/0009-triage-before-autonomy.ja.md) の領域) を市場が
形式化したものであり、市場が独立に収束したグレーディング次元
(スコープ、無許可アクション、行動安定性、オペレーショナル
コントロール) は ADR が accountability 関連とみなす性質群と重なる。
下の Caveat はここにも全面的に適用される: 保険は損失に支払うが、
その行動を誰が承認すべきだったかは決めない。保険が設計層の原則
(承認ゲート・因果追跡可能性) を代替するのか前提とするのかは、
本 repo が判断側で追跡するオープンクエスチョンである。

## 2026 Q2 時点で clean な mechanism instance を持たない ADR

| ADR | clean instance がない理由 |
|---|---|
| [ADR-0001](adr/0001-security-by-absence.ja.md) Security by Absence | 既存 industry mechanism は逆方向に走る (already-broken design への observability vs design 時点での structural absence) |
| [ADR-0003](adr/0003-untrusted-content-boundary.ja.md) Untrusted Content Boundary | clean な mechanism-layer instance なし |
| [ADR-0004](adr/0004-single-external-adapter.ja.md) Single External Adapter | Cross-cloud registry sync は逆方向に走る (multiplied surface への post-hoc observability vs design-time blast-radius bounding) |
| [ADR-0005](adr/0005-human-approval-gate.ja.md) Human Approval Gate | clean な mechanism-layer instance なし |
| [ADR-0009](adr/0009-triage-before-autonomy.ja.md) Triage Before Autonomy | Pre-deployment commitment、runtime mechanism ではない |
| [ADR-0010](adr/0010-phase-separation.ja.md) Phase Separation | Pre-deployment commitment、runtime mechanism ではない |

## Caveat

上記の artifact が ship するのは *mechanism* である: agent の
identity primitive、sub-millisecond ポリシーゲート、cross-vendor
audit infrastructure、sponsor 制度、registry sync。Ship していない
のは *judgment* である: 誰が sponsor になるべきか・それが何の
commitment を意味するか、prohibition-strength hierarchy がなぜ
absence > scaffolding > untrusted boundary の順なのか、autonomous
loop を operation phase から triage out すべきはいつか、deployment
が走っている Phase-crossing decision とは何か。両層は補完関係;
mechanism layer が原理を技術的に enforce 可能にし、judgment layer
(ADR 群) が原理を *いつ・なぜ・どのコストで* enforce すべきかを
記録する。

ここに列挙する vendor 製品は rename / discontinue / restructure
される。それらが対応する ADR はそうならない。製品の進化に応じて本
ファイルを update する; vendor の変更を ADR に逆伝播させてはならない。
