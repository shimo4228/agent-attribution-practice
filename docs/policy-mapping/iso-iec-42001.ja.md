言語: [English](iso-iec-42001.md) | 日本語

# ISO/IEC 42001 AI Management System — AAP ADR への mapping

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
**Framework version:** ISO/IEC 42001:2023 (first edition、
2023-12 公開)。
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** 数年単位 (ISO の正式改訂サイクル、editions
間は典型的に 5 年。amendments はもっと早く出る場合あり)。

> **本記述は AAP の reading である**。以下の per-ADR mapping は、
> AAP の ADR と ISO/IEC 42001:2023 の clause / Annex A control との
> 関係を AAP がどう読んでいるかを記録する。compliance attestation
> ではない: ADR の採用が ISO 42001 control の充足を保証する、
> あるいはその逆を保証する、と AAP は主張しない。legal advice では
> ない。ISO/IEC 42001 の authoritative interpretation は ISO/IEC、
> accredited certification body、qualified compliance counsel に
> 残る。mapping は cross-reference のための citation surface として
> 提供されるものであって、standard text 自体の代替ではない。

## Purpose

ISO/IEC 42001:2023 は **AI Management System (AIMS)** ── AI の
開発、deployment、利用を統治する組織システム ── の要件を規定する
標準で、ISO/IEC 27001 (情報セキュリティ)、ISO 9001 (品質)、ISO
14001 (環境) 等が共有する Annex SL high-level structure (現在の
正式呼称は Harmonized Structure) を基盤とする。本体 clauses (4-10)
は context、leadership、planning、support、operation、performance
evaluation、improvement を扱う。Annex A は AIMS を支える normative
な controls と control objectives を提供する。

ISO 42001 が ship するのは *management-system の形* である ── 組織
が AI を統治するためにどのような process、role、文書、改善ループを
備えるべきか。AAP が記録するのは autonomous-agent subset 向けに
それら process を populate する *judgment layer* である ── 何を
制約するか、その制約はどこに住むべきか、制約が破れた時に誰が結果
を負うか。両 layer は補完関係: ISO 42001 はどの management-system
の piece が存在しなければならないかを伝え、AAP は AI が autonomous
agent である場合にそれらの piece に何が入るべきかを記録する。

本 file で繰り返し使う pattern phrase:

> **ISO 42001 ships the management-system shape. AAP records the
> judgment of what fills that shape** ── 標準は組織が AIMS を運用
> することを要求し、AAP はその中で autonomous-agent の決定を populate
> する。

本 file の外の文脈は [`../adr/README.ja.md`](../adr/README.ja.md)
(ADR 索引)、
[`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md)
(quadrant ごとの control 密度)、
[`./README.ja.md`](README.ja.md) (ディレクトリレベルの規約、
non-attestation disclaimer、revision procedure、
[`../industry-mapping.ja.md`](../industry-mapping.ja.md) との sibling
関係) を参照。

## PDCA × Phase Separation

ISO 42001 は Annex SL 系の他の management-system 標準と同様、
**Plan-Do-Check-Act (PDCA) cycle** を基盤に構築される: 組織は
AIMS を plan し、計画した活動を do し、目標に対して performance を
check し、改善を act する。PDCA は再帰的で iterative — cycle の各
turn がシステムを refine する (PDCA 自体は最近の Annex SL 改訂で
明示 label としては text から外れたが、clause 4-10 の構造は PDCA
mapping を維持しており、authoritative commentary も PDCA で説明
する)。

AAP の Phase 軸 (
[ADR-0010](../adr/0010-phase-separation.ja.md) で導入、2026-05-01)
は関連するが distinct な cut である。PDCA は cycle の各 iteration
内に *flexibility* (planning、新規 do、learning) と *predictability*
(確立された objective に対する check) が共存することを前提とする
のに対し、AAP の design / operation Phase の区別は、autonomous-loop
placement では 2 つの最適化軸が phase 間で **反転** することを記録
する:

- **Design phase** ── path 不明、exploration 必要、最適化軸 =
  flexibility。prototyping、requirements 分析、coding agent / Deep
  Research の各 session を含む
- **Operation phase** ── path 固定、predictability 必要、最適化軸
  = predictability (audit-tracing、attribution、コスト安定性、SLA
  conformance)

ADR-0010 の load-bearing rule ── Autonomous Agentic Loop Quadrant
component を operation phase に置く場合、ADR-0009 の gap-bearer
指名に加え記録された *Phase-crossing decision* が要求される ── は
PDCA 内部で autonomous loop 向けにどのような "Do" phase が実装可能
かに対する制約として位置する。PDCA 自体はこの制約を surface しない;
AAP は operation-phase autonomous loop が generic な PDCA cycle が
named しない構造的コストを伴う、という判断を記録する。

PDCA 構造と AAP Phase 軸は補完関係である: AIMS PDCA cycle は組織が
運用する *managerial* cadence、AAP Phase 軸は cadence 内で
admissible な Quadrant-Phase 組み合わせに関する *deployment-time
の判断* 。

## Per-ADR mapping (ADR ごとの対応)

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.ja.md) は、最強の禁止は
capability を最初から実装しない形である、と記録する。これは ISO
42001 clause 6 (Planning、AI risk assessment と AI risk treatment
を含む) および **AI system life cycle** 関連の Annex A controls に
対応する ── AI の capability surface は deployment 前、design 時に
bound される。標準は AI system capability の定義と文書化を要求する;
AAP は組織が望まない capability に対する最強の treatment はそれを
コードベースから完全に取り除くことであり、audit test は `grep` で
答えが出る形である、という判断を記録する。ISO 42001 は capability
scope を govern する要件を ship する; AAP は *実装しない* ことが
*runtime で制限する* ことと構造的に区別され、treatment 強度の上限
であること、を記録する。

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.ja.md)
は、absence で達成できない禁止は scaffolding 層で決定論的に、LLM
の外側で、該当する全 input に対して 100% 発火する形で enforce する、
と記録する。これは ISO 42001 clause 8 (Operation — operational
controls) および **AI system life cycle** / **use of AI systems**
の Annex A controls に対応する。標準は AI risk treatment のための
operational controls を要求する; AAP は prompt-clause /
convention-file / constitutional enforcement が通常時 50–80%、
敵対的圧力下ではそれ以下しか compliance しないため、構造的に
honest な operational control は non-LLM gate である、という判断を
記録する。

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.ja.md) は、
蓄積された agent state ── agent 自身が author したコンテンツを含む
── は prompt に読み戻される時点で untrusted として扱う、と記録する。
これは **data for AI systems** (data provenance / quality) と
**AI system life cycle** (AI のライフサイクルを通じた内部状態の
取り扱い) の Annex A controls に対応する。標準は AI システムの
data quality と provenance の管理を要求する; AAP は data-quality
boundary に agent 自身の蒸留 output (taint が untrusted な upstream
source から継承される value-chain component) を含めねばならない、
load 時 fail-closed validation と inject 時の明示的
`<untrusted_content>` marker を要求する、という判断を記録する。

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.ja.md) は、各 agent
process が持つ外部 side-effect adapter は高々 1 個であり、blast
radius は設計で限定される、と記録する。これは ISO 42001 clause 6
(Planning — risk treatment) および **AI system life cycle** (システム
設計選択) と **third-party and customer relationships** (adapter
は通常外部システムへの interface) の Annex A controls に対応する。
標準は risk treatment の選択肢の考慮を要求する; AAP は
bound-at-design の方が redirect を保持する (各 agent が 1 adapter
を持ち各 accountability chain を 1 本ずつ持つ) のに対し、
observe-at-runtime はどの adapter がどの side effect を起こしたかを
事後に untangle する必要がある、という判断を記録する。

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.ja.md) は、行動を変える
書き込みは実行前に named human の sign-off を要求する、と記録する。
これは ISO 42001 clause 5 (Leadership — roles、responsibilities、
authorities) と **internal organization** (AI 関連の roles) /
**AI system life cycle** (change control) の Annex A controls に
強く対応する。標準は AI 関連の責任の割り当てと文書化を要求する;
AAP は approval gate が同期的かつ書き込み前であること、stochastic
generation 下では advisory confirmation と preview-then-run が失敗
すること、gate は second LLM ではなく人間で終端しなければならない、
という判断を記録する。

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.ja.md) は、事故後に
operator が何が起きたかを再構成できる ── input、scaffolding
バージョン、approval audit trail、external surface、最終 output
── と記録する。これは ISO 42001 clauses 9 (Performance evaluation、
monitoring / measurement / analysis / internal audit を含む) と
10 (Improvement、nonconformity と corrective action を含む) に
対応する。標準は AIMS performance が measurable であること、
corrective action が nonconformity に従うことを要求する; AAP は
*事故後再構成は build-time の構造的要件であり、runtime observability
practice ではない* ── runtime observability (metric、tracing、
alert) は autonomous-agent の再構成にとって necessary だが
insufficient であり、append-only episode log + version-pin された
scaffolding + ADR-0005 audit trail が再構成を可能にする基盤で
ある、という判断を記録する。

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.ja.md) は、agent の
非 weights 構成要素はすべて operator から visible なファイルとして
存在し、不透明な weight 内には存在しない、と記録する。これは ISO
42001 clause 7 (Support — documented information 要件を含む) と
**AI system life cycle** (configuration / change management) /
**information for interested parties of AI systems** (透明性) の
Annex A controls に対応する。標準は AIMS が AI システムに関する
documented information を維持することを要求する; AAP は
documentation 単独では insufficient であり、behavior は *ファイル
として存在しなければならない* (ファイルの説明として存在するので
ない)、なぜなら weights 内在化 (RLHF、Constitutional AI、instruction
tuning) は behavior を accountable layer から完全に外し、documentation
を ground truth ではなくしてしまうから、という判断を記録する。

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.ja.md) は、各 agent
process が deployment 時に 1 人の identifiable human ── accountable
operator ── と binding される、と記録する。これは ISO 42001 clause
5 (Leadership — roles / responsibilities / authorities の割り当て)
と **internal organization** の Annex A controls に対応する。標準は
AI システムの責任の割り当てと文書化を要求する; AAP は accountability
chain の終端が agent process ごとに 1 人の named human でなければ
ならない (formal rotation は compatible、明示的に log された handoff
付きで)、team や shared role ではならない、なぜなら autonomous agent
下で shared accountability は moral-crumple-zone の失敗を再生産する
から、という判断を記録する。Status: **experimental**。

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.ja.md) は、autonomous-loop
アーキテクチャを採用する前に作業を 4 Business AI Quadrants に沿って
triage すること、Autonomous Agentic Loop Quadrant の選択は deploying
organization が attribution gap を pre-deployment コストとして absorb
する commitment ── deployment 時の identifiable gap-bearer の指名
── になる、と記録する。これは ISO 42001 clauses 4 (Context of the
organization、AIMS の scope の決定を含む)、6 (Planning、AI risk
assessment を含む)、**assessing impacts of AI systems** および
**AI system life cycle** (deployment decisions) の Annex A controls
に対応する。標準は組織に影響評価と informed deployment decision を
要求する; AAP は autonomous loop の deployment decision が *構造的な
attribution gap* を伴い、その gap は事故後の発見ではなく deployment
時に named subject が負わねばならない、という判断を記録する。
Status: **experimental**。

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.ja.md) は、Autonomous Agentic
Loop Quadrant component を operation phase に配置する場合、ADR-0009
の gap-bearer 指名に加えて明示的な *Phase-crossing decision* が要求
される、と記録する。これは ISO 42001 clauses 6 (Planning — risk と
opportunity の treatment) と 8 (Operation — operational planning
and control) および **AI system life cycle** (lifecycle management)
の Annex A controls に対応する。AIMS 周辺の PDCA cycle 自体は design
と operation を最適化軸が反転した phase として区別しない; AAP は
autonomous loop にとってこの区別が load-bearing であり、配置には
記録された deployment-time decision (operation phase 中に浮上した
新パターンを動的に in-place で扱うか、design phase に feedback と
して戻すか) が要求される、という判断を記録する。Status:
**experimental**。

## Per-Quadrant note (Quadrant ごとの注記)

AAP の 4 Business AI Quadrants は problem-space カテゴリであり、agent
カテゴリではない。ISO 42001 は AI 作業をこれらの軸でカテゴリ化しないが、
AAP の quadrant-to-ADR 行列
([`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md))
は ISO 42001 構造にきれいに対応する:

- **Quadrant 1 (Script)** と **Quadrant 2 (Algorithmic Search)** ── AAP
  の対象外、本 file から ISO 42001 mapping は不要
- **Quadrant 3 (LLM Workflow)** ── 6 つの ADR が適用 (0001, 0003,
  0004, 0005, 0006, 0007)、ISO 42001 の PDCA cycle を *中位の governance*
  control 密度で populate する。AIMS improvement loop は component
  粒度で走る
- **Quadrant 4 (Autonomous Agentic Loop)** ── 10 ADR 全てが適用、
  ISO 42001 の PDCA cycle を *高い governance* control 密度で populate
  する。ADR-0008、ADR-0009、ADR-0010 がここで load-bearing、特に ISO
  42001 clauses 5 (Leadership) と 6 (Planning) 下で。improvement loop は
  component 粒度ではなく architectural 粒度で走る ── component-level
  redirect が foreclose されているため
  ([`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md)
  参照)

同じ Annex A control が Quadrant に依存して異なる AAP 側 instantiation
を受ける: "internal organization" control は Quadrant 3 では named LLM-call
role で満たされ、Quadrant 4 では pre-named gap-bearer で満たされる。

## Reverse index — ISO 42001 area → 該当 ADR

| ISO 42001 area | 該当 AAP ADR |
|---|---|
| Clause 4 — Context of the organization | 0009 |
| Clause 5 — Leadership (roles, responsibilities, authorities) | 0005, 0008 |
| Clause 6 — Planning (risk assessment, risk treatment) | 0001, 0004, 0009, 0010 |
| Clause 7 — Support (documented information) | 0007 |
| Clause 8 — Operation (operational controls) | 0002, 0010 |
| Clause 9 — Performance evaluation (monitoring, audit) | 0006 |
| Clause 10 — Improvement (corrective action) | 0006 |
| Annex A — Policies related to AI | 0007, 0009 |
| Annex A — Internal organization (roles for AI) | 0005, 0008 |
| Annex A — AI system life cycle | 0001, 0002, 0003, 0004, 0006, 0007, 0010 |
| Annex A — Data for AI systems | 0003 |
| Annex A — Information for interested parties of AI systems | 0007 |
| Annex A — Use of AI systems | 0002, 0005 |
| Annex A — Third-party and customer relationships | 0004 |
| Annex A — Assessing impacts of AI systems | 0009 |

reverse-index の粒度は意図的に粗い (clause レベルと Annex A area
レベル、個別 control 番号ではない): ISO 42001 Annex A の control 番号
は将来 edition で再編される可能性があるが、AAP 側の判断は renumbering
で shift しない。ISO が Annex A 構成を改訂しても、本 file だけが変わり、
上記 per-ADR mapping は stable。

## Caveat

ISO/IEC 42001:2023 は当該標準の first edition である。改訂版 / amendment
が Annex A controls を再編する、または body-clause 要件を調整する可能性
がある。上記 mapping は各 ISO 改訂と amendment 時に再チェックする。
mapping shift は ADR 自体には伝播しない。ADR と本 file は独立した
cadence で decay し、ADR は durable な anchor として残る。
