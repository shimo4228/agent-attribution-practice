言語: [English](0002-deterministic-prohibition-at-scaffolding.md) | 日本語

# ADR-0002: Deterministic Prohibition at the Scaffolding Layer（足場層での決定論的禁止）

> **Summary。** 危険な capability を absence にできない場合 (ADR-0001)、
> その prohibition は依然として決定論的に enforce されなければならない
> — モデル重み層 (prompt、guidelines、constitutional clauses) ではなく
> scaffolding 層（足場層）(hooks、quarantine、adapter gates) で。
> 不可逆 action に対する確率的制約は装飾でしかない。

## Status
accepted

## Date
2026-04-19

## Context

ADR-0001 は危険な capability の *制限* よりも *不在* を優先する。Absence は
prohibition の最強の形: bypass する対象がない、misconfigure する対象が
ない、patch する対象がない。Audit test は単一の `grep`。

しかしすべての capability を absence にできるわけではない。Agent の
中にはファイルを読んだり、外部サービスを呼んだり、tool output を
観察したりしないといけないものもある。それらに対して残る設計選択は:

1. Prohibition を **scaffolding 層** で enforce する — harness、hook
   system、adapter — LLM の外側で。層は対象 input の 100% で発火する。
   決定は configuration + input から導出可能で、モデル重みからではない。
2. Prohibition を **モデル重み層** で enforce する — system prompt 条項、
   convention ファイル (例えば `CLAUDE.md`)、constitutional 指示。
   遵守は確率的 (通常条件下で約 50–80%、prompt-injection 圧力下では
   それを下回ると観察された)。

「登らないでください」と書かれた看板のある壁は、壁ではない。Prompt
injection、敵対的 input、context 圧力下でのエージェント自身の好奇心
に対して、後者の壁は壁ではない。

このパターンは運用で 2 度観察された:

- `Read` / `Bash` / `Grep` アクセスを生の蓄積 state ファイル (例えば
  episode log) に持つコーディングエージェントは、injected された 1 つの
  指示で untrusted content を ingest するように redirect されうる。
  「log を読まないこと」という convention は確率的コントロール。Path を
  block する PreToolUse hook は決定論的コントロール。同じ agent harness
  がどちらも host できるが、確実に発火するのは hook だけ。
- 蓄積 knowledge に `source_type` を記録し retrieval をそれで weight
  する agent は、memory-injection 系攻撃 (MINJA; Dong et al. 2025,
  [arXiv:2503.03704](https://arxiv.org/abs/2503.03704)) に対して防御を
  持っているように見える。本番監査で、その soft な信頼 weighting は
  *仕掛けはあるが効いていない* ことが判明した — 本番データは migration
  default に支配されており、weighting は一度も discriminate しなかった。
  実際にエージェントを守っていたのは summarization 境界での structural
  quarantine だった: 外部 content は LLM prompt にそもそも入らない。
  Effective な防御は決定論的だった。確率的層は仕事をしていなかった。

両 case の教訓は同じ: *確率的 prohibition は防御に見えるが防御ではない*。

## Decision

Prohibition が absence (ADR-0001) で達成できない場合、それは
**scaffolding 層** で enforce されなければならない、モデル重み層では
なく。Scaffolding 層は capability が発火する前に request を intercept し、
理由付きで block 決定を返す。

### Three implementation patterns（3 つの実装パターン）

| Pattern | 何を block するか | Failure mode |
|---|---|---|
| **Pre-tool hook** | path/argument パターンに match する特定の tool 呼び出し | Matcher が cover しない novel な read path; cover されない subprocess による bypass |
| **Structural quarantine** | Content が LLM prompt にそもそも入ること (例: summarize-then-discard) | Raw content が summary に漏れる producer のバグ |
| **Adapter gate** | 単一 external adapter (ADR-0004) での outbound side effect | Attacker が影響を与えた destination や payload を accept する adapter logic |

3 つすべてが 1 つの性質を共有する: **決定が LLM を参照しない**。
正当性は configuration + gating コードを読めば検証できる。

### The prohibition-strength hierarchy（禁止強度の階層）

Prohibition に関する 3 本の ADR が強度の階層を成す:

| Layer | ADR | Mechanism | 主な failure mode |
|---|---|---|---|
| Absence | ADR-0001 | Capability が最初から実装されない | 後の patch が capability を追加 |
| Scaffolding enforcement | ADR-0002 (これ) | Capability は存在するが non-LLM な check で gate される | Matcher の gap、misconfiguration、novel な bypass path |
| Untrusted boundary | ADR-0003 | Content が LLM に届くが、untrusted として包まれ・注釈される | LLM の遵守は確率的 |

Prohibition を設計する時は階層を上から歩く。現層が capability を
本当に抱えきれない時だけ次の層に降りる。

### What this does NOT claim（これが主張しないこと）

- **Absence と等価ではない。** Hook は disable され、misconfigure され、
  matcher が cover しない path で bypass されうる。Absence にはこれと
  等価な failure mode がない。
- **Values の置き換えではない。** 連続的判断 — トーン、配慮、バイアス
  検出、文脈的妥当性 — に対しては、モデル重み層が利用可能な唯一の層。
  この ADR は *カテゴリ的禁止* についてのもので、values についてでは
  ない。
- **許可の license ではない。** Absence の代わりに scaffolding
  enforcement を選ぶことは譲歩であって default ではない。問い *「この
  capability は代わりに absence にできなかったか?」* は gate された
  すべての capability について生き続ける。

### Applying the principle（原理の適用）

「agent は X をしてはいけない」という rule を convention、prompt 条項、
constitutional 指示として書く前に:

1. X を absence にできるか (ADR-0001)? Yes ならそうする。
2. できないなら、scaffolding 層で intercept できるか? Yes ならそう
   する。Matcher、bypass surface、residual risk を文書化する。
3. どちらも達成できないなら、prohibition は確率的。values guidance
   として扱い、保証として扱わない。Control だと主張しない。

## Alternatives Considered

- **Convention ファイルのみ** (例えば `CLAUDE.md` の「log を読まない
  こと」)。敵対的条件下、および通常の運用圧力下 (debugging、好奇心) で
  fail することが観察された。Documentation としては有用、enforcement
  としては有用ではない。
- **System prompt / constitutional clause。** Prompt injection、context
  圧縮による条項の脱落、context 依存のモデル遵守の対象。構造上、確率的。
- **Trust-weighting retrieval** (「危険な source を deprioritize する」)。
  Schema 上は防御に見える; 本番では discriminate する signal は通常
  migration default や producer 不在で flooded され、装飾になる。
- **Allow and audit** — action を許可し、事後に検出。Reversible な
  action で blast radius が低いものには appropriate。不可逆なものには
  unacceptable。

## Consequences

- 新しいカテゴリの artifact がプロジェクトに入る: **scaffolding 層の
  enforcement スクリプトと quarantine 境界**。Version 管理され、
  auditable で、モデルから独立にテスト可能。
- Residual risk が **列挙可能** になる: 各 hook について、matcher の
  coverage gap と既知の bypass path が書き出せる。これは確率的制約と
  対照的 — 確率的制約の residual risk は *「モデルが遵守しないかも
  しれない」* としか述べられず、これは risk register ではなく no
  control の宣言である。
- 違反が **追跡可能** になる: *hook は発火したか? No なら matcher は
  なぜ miss したか? Yes ならどうやって bypass されたか?* — 対照的に
  *「モデルが指示に従わなかった」* には診断可能な原因がない。
- Convention-as-security が **格下げ** される。Convention ファイルと
  prompt 層の指示は values と documentation のためにはなお有用だが、
  prohibition のために頼ることはなくなる。
- Agent に grant される新しい capability ごとに同じ問いが triggered
  される: *この capability の周りの prohibition のための scaffolding
  gate はどこか?* Gate なしで追加された capability は prohibition なし
  で追加された capability。

---

**Lineage。** PreToolUse hook パターンは
[contemplative-agent](https://github.com/shimo4228/contemplative-agent)
で運用中で、コーディングエージェントの生 JSONL episode log への
アクセスを block する 3 つの hook (`block-episode-logs-{read,bash,grep}.sh`)
を ship している; 付随する
`docs/security/2026-04-01-episode-log-access-control.md` が convention
ファイル rule (確率的) から決定論的 hook への upgrade を文書化する。
Quarantine-boundary パターンは同じプロジェクトの `summarize_record`
関数 — MINJA 系のメモリ注入に対する実際の主要防御で、ADR-0021 実装
監査 (contemplative-agent `docs/evidence/adr-0021/implementation-audit-20260418.md`、
ADR-0028、ADR-0029) で確立された。本 ADR はそれらの発見から一般原理を
抽出し、2026-04-19 に本 repository で初めて formalize された。

**Industry instantiation (informational, 2026 Q2)。**
[Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)
(2026-04-02、MIT-licensed open source) は *Agent OS* を ship する —
全 agent action を intercept するサブミリ秒・stateless ポリシーエンジン
で、OWASP Agentic Top 10 への対応を謳う。本 ADR の原理の mechanism
instance として、model-weight 遵守ではなく scaffolding 層で発火する
決定論的 gate を提供する。提供していないのは、どの prohibition が
absence 層 (ADR-0001) / scaffolding 層 (本 ADR) / untrusted-content
層 (ADR-0003) に属するかを決める
[prohibition-strength hierarchy](../glossary.ja.md#prohibition-strength-hierarchy禁止強度の階層)
の判断; 製品資料は全 prohibition を scaffolding 層の concern として
扱う。両層は補完関係: toolkit が原理を技術的に enforce 可能にし、
本 ADR が原理を *いつ・なぜ・どのコストで* enforce すべきかを記録する。
[`../inspiration.md`](../inspiration.md#industry-artifacts-that-instantiate-aap-principles)
参照。
