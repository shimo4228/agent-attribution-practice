言語: [English](0007-scaffolding-visibility.md) | 日本語

# ADR-0007: Scaffolding Visibility（足場の可視性）

> **Summary。** Agent の非 weights 部品はすべて — system prompt、persona、
> rules、identity、tool 定義、agent loop、harness logic — **scaffolding
> （足場）**。Scaffolding は version 管理下のファイルとして実体化され、
> operator が inspect 可能で、approval gate を通じてのみ変更可能でなけれ
> ばならない。Operator レベルの可視性は交渉不可。External 可視性は別の
> 問いで、それ自身の trade-off を持つ。

## Status
accepted

## Date
2026-04-14 (articulated) / 2026-04-19 (本 repository で ADR 化)

## Context

AI agent は振る舞いを形作る 2 つの層を持つ:

- **Model weights** — LLM が事前訓練と事後訓練 (RLHF、Constitutional
  AI、instruction tuning) で学んだもの。ここの変更は訓練を要する;
  内容は構造上 opaque。
- **Scaffolding** — weights の外で振る舞いを依然形作るすべて: system
  prompt、persona、rules、skills、tool 定義、RAG index、agent loop、
  safety gate、session 管理、runtime harness。ここの変更は通常の
  ファイル編集。

業界トレンドは scaffolding を内側に動かすこと。Chain-of-Thought は
外部 prompt (「Let's think step by step」) として始まり、reasoning
モデルの weights に absorb されてきた。System prompt は事後訓練 run
に distill される。Behavioral rule は訓練データの Constitutional AI
条項になる。Scaffolding 層は縮み、weights 層は育つ。

これには 2 つの帰結がある:

1. **技術的不透明性。** Weights に符号化された振る舞いは `grep` で
   読めない。Version 化、diff、revert を再訓練なしにできない。Postmortem
   が「これを引き起こした行」を指せない。
2. **商業的秘匿。** Weights の外に残る scaffolding は競争上の理由で
   隠されることが多い。Prompt engineering は差別化要因; published な
   scaffolding は競合がコピーできる scaffolding。2026-03-31 に最も
   safety を重視する AI 企業の 1 社で起きた偶発的なソースコード開示は、
   そこですら scaffolding を公表していなかったことを示した — 競争
   環境がそれを許さなかった。

結果: agent の振る舞いを形作る両方の層が構造的に見えにくい。
Accountability-wall thesis (ADR-0005 と付随する essay 参照) は振る舞い
が causally traceable であることを要求する。読めない層を通じて原因を
辿ることはできない。

## Decision

モデル weights *ではない* すべての部品が scaffolding であり、
scaffolding は以下でなければならない:

1. **ファイルとして実体化** — identity、rules、constitution、skills、
   tool 定義、agent-loop logic、prompt template。振る舞いを形作る部品
   が runtime variable、proprietary な API response、mutable な
   in-memory cache だけに住むことはない。
2. **Version 管理** — 変更が diff 可能、revert 可能、特定の commit
   (または等価な record) に attributable。
3. **Operator が inspect 可能** — agent を動かす者が誰であれ、自分が
   動かしている instance の完全な scaffolding を読める。Operator が
   deploy するのは trust されているが読むのは trust されていない、
   という「private」scaffolding は存在しない。
4. **書き込み時に gate される** — Behavior-modifying scaffolding の
   変更は ADR-0005 (Human Approval Gate) を流れる。Silent な書き換え
   なし。

### Operator visibility vs external visibility（operator 可視性 vs 外部可視性）

2 つの異なる可視性の問いを混同してはならない:

| Question | Position |
|----------|----------|
| Operator は自分が動かす agent の完全な scaffolding を読めるか? | **常に Required。** |
| 外部 reviewer / user / auditor は読めるか? | 別の問い。正当な商業的 trade-off を持つ。 |

この ADR は 2 番目の問いに position を取らない。商業 operator は
scaffolding を public view から redact する正当な理由を持ちうる。この
ADR が禁ずるのは、*商業的秘匿を operator からも隠す理由として使う
こと* — そうすると loop 内の人間が変更を review できなくなり、
ADR-0005 と ADR-0006 が enforce 不能になる。

### What "materialized as files" rules out（「ファイルとして実体化」が排除するもの）

- Base model が符号化したものを超えてモデル weights に焼き付けられた
  scaffolding。RLHF が operator がファイルとして読み出せない behavioral
  rule を加えるなら、その rule は scaffolding 層を escape して
  unaccountable になる。(この ADR は訓練データの透明性を要求しない。
  *この deploy された agent に適用されている* 振る舞いが scaffolding
  層に残ることを要求する。)
- Operator が inspect できる対応 artifact のないまま vendor policy
  としてのみ維持される「built-in」 safety 振る舞い。
- Proprietary な SDK 呼び出しから初期化された runtime variable として
  のみ表現される rule。

### What it does not rule out（排除しないもの）

- Closed-weights foundation model の使用。Weights は構造上 opaque;
  この ADR は weights 自体ではなく weights の周りの scaffolding を
  扱う。
- Scaffolding を public publication から redact すること。Operator
  可視性が要件; public 可視性は別の選択。
- File レベルの scaffolding を runtime 形式で mirror する性能最適化
  (cache、compilation)。File が canonical。

### Minimum set for causal tracing（因果追跡の最小集合）

「可視 vs 隠蔽」の解は「全部 publish」でも「全部隠す」でもない。それは
causal tracing を可能にする **最小集合** を定義すること。少なくとも:

- Incident 当時にどの scaffolding バージョンが active だったか
- どの input が agent に届いたか (ADR-0006)
- どの approval record が active scaffolding バージョンを gate したか
  (ADR-0005)

組織はこの最小集合を、scaffolding の全文を開示せずに開示しうる。
開示の line をどこに引くかは operator-specific。Line の下には
accountability chain がそもそも存在しない。

## Alternatives Considered

- **現代の agent は部分的に weights-internalized であると受け入れ、
  振る舞いを外部で計測する** — Operator 層の原則として reject。外部
  振る舞いテストは evaluation には有用、postmortem の causal tracing
  には不十分。テストは agent が *ある振る舞いをする* ことを教えるが、
  特定 input でなぜそうしたかを教えない。
- **Scaffolding は商業資産; operator 可視性は optional** — Reject。
  Operator が scaffolding を読めなければ、human approval (ADR-0005) と
  causal traceability (ADR-0006) は enforce できない。Optional な
  可視性は可視性ではない。
- **すべての scaffolding の public 開示を要求する** — Overreach として
  reject。Operator 可視性が load-bearing requirement; public 可視性は
  正当な trade-off を持つ別の dimension。
- **Scaffolding 内容に対する vendor 証言を信頼する** — Reject。
  Inspectable artifact のない attestation は signpost pattern
  (ADR-0005 context 参照)。

## Consequences

- Scaffolding を expose することを refuse する vendor の上に agent を
  build するには、unaccountable な層を受け入れる (ADR-0006 違反) か
  別の vendor を選ぶしかない。
- 小規模 operator と個人 developer — scaffolding を隠す商業的理由が
  ない — はこの ADR を default で満たす。Existence proof はその側から
  来る。
- 「健全な scaffold dissolution」(scaffolding が clear な会話 pattern /
  読みやすいファイルに溶け込む) と「不健全な internalization」
  (scaffolding が読み戻せない weights に absorb される) の境界が、この
  ADR が境界を引く場所。[`../glossary.ja.md`](../glossary.ja.md) 参照。
- Postmortem が可能になる。「どの scaffolding バージョンが active
  だったか」が答えを持つ。

---

**Lineage。** 分析は
[AI Agent Black Boxes Have Two Layers: Technical Limits and Business Incentives](https://github.com/shimo4228/zenn-content/blob/main/articles-en/agent-blackbox-capitalism-timescale-en.md)
(2026-04-14、trilogy の 3 本目) で展開された。Scaffolding/weights
区別と operator 側 file 実体化の早期言語化は contemplative-agent の
design evolution に登場した。Spine の context は
[`../inspiration.md`](../inspiration.md) を参照。

**Industry instantiation (informational, 2026 Q2)。** Microsoft が
[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01) と共に named した *Shadow AI* framing は、本 ADR が
記録する不在問題 ── scaffolding が visible でないと governance が
適用できない ── を業界レベルで認識した証拠。Microsoft Defender と
Intune は Windows デバイス上の local agent (OpenClaw、GitHub Copilot
CLI、Claude Code) を既に検出しており、2026-06 のリリースで資産
コンテキストマッピング (各 agent がどのデバイスで動作し、どの MCP
server に接続し、どのクラウドリソースに触るか) の追加が予定されて
いる。Mechanism instance としては検出側の visibility — 以前は invisible
だった scaffolding を operator から発見可能にする。提供していないのは、
scaffolding visibility が weights internalization の構造的代替で
あるという argument、*healthy dissolution* (scaffolding が clear な
会話 pattern / 読みやすいファイルに溶け込む) と *unhealthy
internalization* (scaffolding が weights に吸収される) の境界、本 ADR
が引く operator-vs-public visibility の区別。両層は補完関係: Microsoft
が Shadow AI gap を運用的に表面化させ、本 ADR が **なぜその gap が
重要か、どこに visibility line を引くべきか** を記録する。
[`../inspiration.md`](../inspiration.md#industry-artifacts-that-instantiate-aap-principles)
参照。
