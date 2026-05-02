言語: [English](0006-causal-traceability.md) | 日本語

# ADR-0006: Causal Traceability（因果追跡可能性）

> **Summary。** Incident の後、*どのバージョンの agent が、どの input に
> 応答して、どんな output を produce し、誰が承認したか* を再構成できな
> ければならない。これは runtime では達成できない — build time で
> artifact 層に design されなければならない。Append-only log、versioned
> behavior-modifying artifact、approval gate からの audit trail が最低限
> の substrate。

## Status
accepted

## Date
2026-04-13 (first articulated) / 2026-04-19 (本 repository で ADR 化)

## Context

LLM ベースの agent における典型的な production incident は、誰かが
incident report を書くことで終わる。「Root cause」フィールドが system の
形が見えてくる場所。

通常の agent はこのステップで失敗する: 最終 LLM output と external API
呼び出しのリストは log されるが、agent が *なぜ* そう決めたか — どの
prompt fragment が load-bearing だったか、どのバージョンの identity や
rules が active だったか、どの distilled knowledge が context に流れ
込んだか — は復元不能。残るのは output 文字列と非構造化 conversation
log。

組織は人間の決定について類似の問題を数百年かけて解いてきた: PR review、
CAB、audit trail、version control、postmortem プロセス。各々が *chain*
を作り、incident から決定へ逆向きに歩ける。今日の agent はその等価物
なしで deploy されることが多い。

この原理は要件を named する: あらゆる behavior-modifying event は、
build time に既に存在する artifact を通じて事後に復元可能でなければ
ならない。

## Decision

Agent の artifact 層は *post-incident causal reconstruction* を一級
要件として支援しなければならない。具体的には:

### What must be recoverable（何を復元できるべきか）

任意の incident について、operator は以下に答えられねばならない:

1. **どの input が agent に届いたか?** 完全な episode record、逐語的に。
2. **agent の振る舞いのどのバージョンが active だったか?** Identity、
   rules、constitution、skills が当該時点の特定バージョンに pin されて
   いる。
3. **どの approval が active バージョンを gate したか?** バージョンを
   人間の承認者 (または却下者) と timestamp に link する audit record。
4. **どの external surface に触れたか?** 1 つの adapter (ADR-0004)、
   1 つの destination、event ごとに記録される。
5. **agent は何を output したか?** 最終 output が、それを produce した
   input と共に保存される。

### Required artifacts（必須 artifact）

| Artifact | Property |
|----------|----------|
| Episode log | Append-only、1 event 1 行、atomic write |
| Identity / rules / constitution | Version 固定; 書き込みは ADR-0005 approval gate を通る |
| Audit trail | あらゆる approval 決定 (accept、reject、手動 CRUD) と reason |
| Architecture Decision Records | 現状の構造が存在する理由; system の進化と共に更新 |
| Adapter call log | 単一の pin された adapter に scope された request/response pair |

### Append-only is structural, not advisory（Append-only は構造であって advisory ではない）

Episode log を rotate、truncate、削除することは agent が実際に見たもの
の唯一の完全な記録を破壊する。Log は system property として append-only
でなければならない (umask、write-once 規律、必要なら truncate 前に
backup)。Storage 増加は払うべきコストであって、削除の言い訳ではない。

### Versions, not snapshots（snapshot ではなく version）

Behavior-modifying artifact は version 化される、「incident X が起きた
時にどの rules が active だったか」が決定的な答えを持つように。In-place
で上書きされる mutable ファイルは、他のすべてのピースが intact でも
causal traceability を壊す。

## Alternatives Considered

- **Runtime observability のみ** (metrics、tracing、alert) — 必要だが
  不十分。Runtime データは「何かが起きた」ことを capture する; causal
  traceability が必要とするのは「なぜ」で、これは当時の versioned
  artifact state に依存する。
- **Development 環境で incident を replay する** — 確率的生成に対して
  魅力的だが妥当でない。Replay は異なる output を生む。必要なのは
  実際の決定の record で、再構成ではない。
- **集中 logging service** (ELK、Splunk 等) — 有用な infrastructure だが、
  この決定とは直交。決定は *何を* どんな構造で log するかであり、
  どこに保存するかではない。
- **LLM の自己説明を信頼する** — Reject。確率的モデルの事後合理化は
  narrative であって原因ではない。

## Consequences

- Incident が分析可能になる。「agent はなぜ X したのか?」に推測ではなく
  手続きが対応する。
- Accountability が分配可能になる。Incident chain がどの人間がどの
  バージョンを承認したか、どのコードがどの capability (またはその不在)
  を導入したかを identify する。Blame は消去法で on-call engineer に
  default しない。
- 第三者 (監査人、規制当局、組織レビュアー) が agent を動かさずに
  chain を inspect できる。これは high-stakes 環境での採用の前提条件。
- Storage cost が育つ。Append-only + version pin は archive tier を
  追加しない限り system が履歴を無期限に蓄積することを意味する。これは
  意図的。
- 開発スタイルが shift する。「postmortem はどう見えるか」を最初から
  design するのは、最初の incident 後に traceability を retrofit する
  より安い。

### Convergence with older disciplines（古い disciplines への収斂）

必須ピースは組織が人間の決定に使うものと構造的に同一:

| Organizational practice | Traceability equivalent |
|-------------------------|-------------------------|
| 職務分掌 | Single External Adapter (ADR-0004) |
| 四眼原則 | Human Approval Gate (ADR-0005) |
| 最小権限 | Security by Absence (ADR-0001) |
| Internal controls | Pre-write approval audit |
| Change Advisory Board | Versioned behavior-modifying artifacts |
| Audit log | Append-only episode log |

この収斂は装飾ではない。人間にこれらの実践を採用させた決定コストの
非対称性 (incident は upstream の投資より桁違いに高くつく) は、
agent にも同様にあてはまる。

---

**Lineage。** Thesis は
[Can You Trace the Cause After an Incident?](https://github.com/shimo4228/zenn-content/blob/main/articles-en/agent-causal-traceability-org-adoption-en.md)
(2026-04-13、trilogy の 2 本目) で書き上げられた。Trilogy と
architectural-follow-up spine の context は
[`../inspiration.md`](../inspiration.md) を参照。

**Industry instantiation (informational, 2026 Q2)。**
[Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)
(GA 2026-05-01) の一部として ship された
[Microsoft Purview AI Data Security Investigations](https://learn.microsoft.com/en-us/microsoft-agent-365/overview)
は、Microsoft 365 / Azure / サードパーティ AI service のログを相関
させ AI interaction の data lifecycle を再構成する; AI-sensitive な
情報タイプの自動分類、保持ラベル、DLP ポリシー適用を備える。本 ADR
の causal traceability 要件の cross-vendor mechanism instance として、
「あらゆる event が事後に再構成可能」 性質に最も近い商業製品。
提供していないのは、なぜ causal traceability が runtime observability
practice ではなく build-time structural requirement でなければ
ならないかの argument (本 ADR の core point)、および、どの approval
がどの scaffolding バージョンを gate したか (再構成が named human で
終端するために必要な ADR-0005 と紐づく audit trail) の記録。両層は
補完関係: Purview が cross-vendor 再構成を技術的に可能にし、本 ADR が
**どこで終端しなければならないか** を記録する。
[`../inspiration.md`](../inspiration.md#industry-artifacts-that-instantiate-aap-principles)
参照。
