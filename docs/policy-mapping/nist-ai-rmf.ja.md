言語: [English](nist-ai-rmf.md) | 日本語

# NIST AI Risk Management Framework — AAP ADR への mapping

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
**Framework version:** NIST AI Risk Management Framework 1.0
(NIST.AI.100-1, 2023-01) + Generative AI Profile (NIST.AI.600-1,
2024-07).
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** 年単位の見直し (GAI Profile は core RMF
より改訂頻度が高い見込み)。

> **本記述は AAP の reading である**。以下の per-ADR mapping は、
> AAP の ADR と NIST AI RMF (および該当する場合は Generative AI
> Profile) の function / category との関係を AAP がどう読んでいる
> かを記録する。compliance attestation ではない: ADR の採用が
> NIST function の充足を保証する、あるいはその逆を保証する、と
> AAP は主張しない。legal advice ではない。NIST AI RMF の
> authoritative interpretation は NIST と qualified compliance
> counsel に残る。mapping は cross-reference のための citation
> surface として提供されるものであって、framework text 自体の
> 代替ではない。

## Purpose

NIST AI RMF 1.0 は AI リスクマネジメントを 4 つの function ──
**GOVERN, MAP, MEASURE, MANAGE** ── を中心に組織化する。各 function
は category と subcategory を持ち、組織が自前の control で populate
する。Generative AI Profile (NIST.AI.600-1) は generative / dual-use
AI システムに固有のリスクカテゴリ (confabulation、information
integrity、human-AI configuration、value-chain integration 等) を
追加し、各所で RMF function を参照する (公式 category 名は本 file 末尾の reverse index 表を参照)。

NIST RMF が ship するのは *構造* である ── 何を govern するか、何を
map するか、何を measure するか、何を manage するか。AAP が記録する
のは *判断* である ── 何を制約するか、その制約はどこに住むべきか、
制約が破れた時に誰が結果を負うか。両 layer は補完関係: NIST はどの
問いを軸に組織化すべきかを伝え、AAP は autonomous-agent 運用に根ざ
した形でその問いの特定 subset への answer を記録する。

本 file で繰り返し使う pattern phrase:

> **NIST RMF ships X. AAP records the judgment of when, why, and
> at what cost X should be enforced** ── framework は具体的な
> control 選択を組織に委ねており、AAP はその選択を autonomous-agent
> subset について populate する。

本 file の外の文脈は [`../adr/README.ja.md`](../adr/README.ja.md)
(ADR 索引)、
[`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md)
(quadrant ごとの control 密度)、
[`./README.ja.md`](README.ja.md) (ディレクトリレベルの規約、
non-attestation disclaimer、revision procedure、
[`../industry-mapping.ja.md`](../industry-mapping.ja.md) との sibling
関係) を参照。

## Per-ADR mapping (ADR ごとの対応)

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.ja.md) は、最強の禁止は
capability を最初から実装しない形 ── policy review ではなく `grep`
で答えが出る形 ── である、と記録する。これは最も直接的には **GOVERN**
function (AI capability scope に対する組織の posture) と **MAP**
function (システム境界の文書化) に対応する: システムの capability
surface は harness が ship する内容で定義されるのであって、runtime
control で定義されるのではない。Generative AI Profile では、ADR-0001
は **Dangerous, Violent, or Hateful Content** および **Information
Security** リスクカテゴリへの treatment に貢献する ── 本来 manage
する必要のあったリスクの capability surface 自体を取り除く形で。NIST
RMF は組織が capability scope を govern *するか* の問いを ship する;
AAP は absence が restriction より構造的に強いこと、audit test は
`grep` で答えが出る形であること、という判断を記録する。

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.ja.md)
は、absence が達成できない場合、次に強い禁止は scaffolding 層
(PreToolUse hook、structural quarantine、adapter gate) での決定論
的 enforcement ── LLM の外側で、該当する全 input に対して 100%
発火する ── である、と記録する。これは **MANAGE** function (特に
risk treatment / risk response 周りの category) に対応する: management
decision を実装する enforcement mechanism が、確率的ではなく決定論
的であることが要求される。Generative AI Profile では、ADR-0002 は
**Information Security** (prompt injection 耐性) と **Confabulation**
(confabulated 推論が引き起こしうる未承認 write の防止) の treatment
を強化する。NIST RMF は組織に enforcement mechanism の選択を許す;
AAP は prompt-clause / convention-file / constitutional 形式の
enforcement が通常時 50–80%、敵対的圧力下ではそれ以下しか compliance
しないこと、構造的に honest な treatment は non-LLM gate であること、
という判断を記録する。

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.ja.md) は、
蓄積された agent state ── episode log、knowledge store、蒸留 identity、
agent 自身の過去 output ── は prompt に読み戻される時点で untrusted
content として扱う、と記録する。これは **MAP** function (data flow と
trust boundary の文書化) と **MANAGE** function (蓄積状態由来リスク
の treatment) に対応する。Generative AI Profile では、ADR-0003 は
**Data Privacy**、**Information Integrity**、**Value Chain and
Component Integration** のリスクカテゴリに対応する ── agent 自身の
蒸留 output は value-chain component であり、その taint は untrusted
な upstream source から継承される。NIST RMF は組織に trust boundary
の同定を要求する; AAP は boundary に agent 自身を含めねばならない
こと、load 時 fail-closed validation と inject 時の明示的
`<untrusted_content>` marker を要求する、という判断を記録する。

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.ja.md) は、各 agent
process が持つ外部 side-effect adapter は高々 1 個であり、blast
radius は runtime 観測ではなく設計で限定される、と記録する。これは
**GOVERN** function (system architecture governance、decommissioning
planning) と **MAP** function (影響範囲のカテゴリ化) に対応する。
Generative AI Profile では、ADR-0004 は **Information Security**、
**Value Chain and Component Integration**、**Human-AI Configuration**
の各リスクカテゴリへの曝露を design 時点で surface count を縮約する
ことで同時に低減する。NIST RMF は *設計時に bound* と *runtime で
observe* の双方を有効な risk treatment として認める; AAP は
bound-at-design の方が redirect を保持する (各 agent が 1 adapter
を持ち各 accountability chain を 1 本ずつ持つ) のに対し、observe-at-runtime
はどの adapter がどの side effect を起こしたかを事後に untangle する
必要がある、という判断を記録する。

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.ja.md) は、agent の
skill / rule / identity / constitution を改変するすべての command
が、書き込み前に named human の明示承認を要求し、`--auto` flag は
提供されない、と記録する。これは **GOVERN** function (roles /
responsibilities / accountability structure を扱う category) と
**MANAGE** function (人間レビューを要求する treatment) に直接対応
する。Generative AI Profile では、ADR-0005 は **Human-AI
Configuration** リスクカテゴリ (behavior-changing decision に対する
named human の oversight) に対応する。NIST RMF は oversight におけ
る人間の役割の確立を要求する; AAP は gate が同期的かつ書き込み前
であること、stochastic generation 下では advisory confirmation と
preview-then-run が失敗すること、gate は second LLM ではなく人間で
終端しなければならないこと (second LLM は prompt injection 下で
fail open する確率的 gate)、という判断を記録する。

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.ja.md) は、事故後に
operator が agent に到達した input、active だった scaffolding
バージョン、そのバージョンを gate した承認、touch した external
surface、最終 output を再構成できる、と記録する。これは **MEASURE**
function (AI リスク追跡のメカニズム、事故後分析) と **MANAGE**
function (incident response) に強く対応する。Generative AI Profile
では、ADR-0006 は **Information Security** と **Information
Integrity** リスクカテゴリを各 event の事後再構成を可能にする形で
支える。NIST RMF は再構成能力を runtime observability (telemetry、
metric、alert) として実装するか build-time structural (append-only
log、version-pin された scaffolding、ADR-0005 gate からの audit
trail) として実装するかを組織の選択に委ねる; AAP は autonomous-agent
の再構成には runtime observability では necessary だが insufficient
であり、structural form が要求される、という判断を記録する。

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.ja.md) は、agent の
非 weights 構成要素 ── system prompt、persona、rule、identity、
tool definition、RAG index、agent loop、safety gate、runtime
harness ── がすべて scaffolding であり、version control 配下の
ファイルとして materialize され、operator から inspect 可能で、
ADR-0005 承認 gate を通してのみ変更可能、と記録する。これは
**GOVERN** function (system documentation、change management) と
**MEASURE** function (risk measurement の対象となるシステムの
auditability) に対応する。Generative AI Profile では、ADR-0007
は behavior を RLHF / Constitutional AI / instruction tuning で
model weights に内在化させずに accountable layer に保つ形で
**Information Integrity** と **Human-AI Configuration** に貢献
する。NIST RMF は system documentation を要求する; AAP は
documentation 単独では insufficient であり、behavior は *ファイル
として存在しなければならない* (ファイルの説明として存在するので
ない)、weights 内在化した behavior は accountable layer から完全
に出ていく、という判断を記録する。

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.ja.md) は、各 agent
process が deployment 時に 1 人の identifiable human ── 当該 agent
の accountable operator ── と binding される、と記録する。これは
**GOVERN** function (role / responsibility / accountability chain
を扱う category、典型的には GOVERN 2.x 系) に対応する。Generative
AI Profile では、ADR-0008 は **Human-AI Configuration** リスク
カテゴリに対応する ── oversight chain を team / role / committee
ではなく named individual で終端する形で。NIST RMF は AI actor の
役割が identify され accountable であることを要求する; AAP は
chain の終端が agent process ごとに 1 人の named human でなければ
ならない (formal rotation は compatible、明示的に log された
handoff 付きで)、team や shared role ではならない、なぜなら
autonomous agent 下で shared accountability は moral-crumple-zone
の失敗を再生産するから、という判断を記録する。Status:
**experimental** ── binding pattern は構造的に honest だが、多様な
組織設定で validation されていない。

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.ja.md) は、autonomous-loop
アーキテクチャを採用する前に作業を 4 Business AI Quadrants に沿って
triage すること、autonomous loop は Autonomous Agentic Loop Quadrant
に予約され、その採用は deploying organization が attribution gap を
pre-deployment コストとして absorb する commitment ── identifiable
gap-bearer の指名 ── になる、と記録する。これは NIST の **GOVERN**
function (AI リスクに対する組織の posture) への最も深い AAP-side の
load-bearing 貢献である: NIST は組織に AI を *deploy するかしないか、
どう deploy するか* を *決定* せよと求める; AAP は autonomous loop の
deployment decision は構造的な attribution gap を伴い、その gap は
事故後の発見ではなく deployment 時に named subject が負わねばならない、
という判断を記録する。Generative AI Profile では、ADR-0009 は generative
agent の deployment-decision 層に位置し、component-level redirect が
foreclose された時に誰が答えるかを記録する形で **Human-AI Configuration**
と **Value Chain and Component Integration** に対応する。Status:
**experimental**。

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.ja.md) は、Autonomous Agentic
Loop Quadrant component を operation phase に配置する場合、ADR-0009 の
gap-bearer 指名に加えて明示的な *Phase-crossing decision* が要求される、
と記録する ── 「operation 中に新パターンが浮上した時、autonomous loop が
動的に in-place で扱うか、design phase に feedback として戻すか」。
両答とも admissible、「その時に決める」は不可。これは **GOVERN**
function (AI lifecycle に対する組織 governance) と **MANAGE** function
(operation 中に浮上するリスクの treatment) に対応する。Generative AI
Profile では、ADR-0010 は operation / lifecycle 境界における
**Information Integrity**、**Human-AI Configuration**、**Value Chain
and Component Integration** リスクカテゴリに対応する ── これらの
リスクカテゴリは design phase (path 不明) と operation phase (path
固定) で異なる形を取る。NIST RMF は design と operation を最適化軸が
反転した別フェーズとして特に区別しない; AAP は autonomous loop に
とってこの区別が load-bearing であり、配置には記録された deployment
時の decision が要求される、という判断を記録する。Status:
**experimental**。

## Per-Quadrant note (Quadrant ごとの注記)

AAP の 4 Business AI Quadrants は problem-space カテゴリであり、agent
カテゴリではない。NIST RMF は AI 作業をこれらの軸でカテゴリ化しないが、
AAP の quadrant-to-ADR 行列
([`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md))
は NIST の構造にきれいに対応する:

- **Quadrant 1 (Script)** と **Quadrant 2 (Algorithmic Search)** ── AAP
  の対象外、本 file から NIST mapping は不要
- **Quadrant 3 (LLM Workflow)** ── 6 つの ADR が適用 (0001, 0003,
  0004, 0005, 0006, 0007)、NIST の **GOVERN**、**MAP**、**MEASURE**、
  **MANAGE** を *中位の governance* control 密度で populate する
- **Quadrant 4 (Autonomous Agentic Loop)** ── 10 ADR 全てが適用、
  NIST function を *高い governance* control 密度で populate する。
  ADR-0008、ADR-0009、ADR-0010 がここで load-bearing、特に accountability
  structure と incident response 周りの NIST **GOVERN** category 下で

Generative AI Profile の *Human-AI Configuration* リスクカテゴリは
Quadrant 3 (役割名付き LLM 呼び出し、決定論的制御フロー) と Quadrant 4
(pre-named gap-bearer + Phase-crossing decision) で **大きく異なる
control 密度** で満たされる; 同じ NIST 条項が Quadrant に依存して異
なる AAP 側の instantiation を受ける。

## Reverse index — NIST function → 該当 ADR

| NIST function (主要 category) | 該当 AAP ADR |
|---|---|
| **GOVERN** ── 組織の posture、accountability structure、roles、decommissioning | 0001, 0004, 0005, 0007, 0008, 0009, 0010 |
| **MAP** ── context、システム境界、data flow、trust boundary | 0001, 0003, 0004 |
| **MEASURE** ── risk tracking、auditability、事故後分析 | 0006, 0007 |
| **MANAGE** ── risk treatment、incident response、lifecycle management | 0002, 0003, 0005, 0006, 0010 |

| Generative AI Profile リスクカテゴリ | 該当 AAP ADR |
|---|---|
| Dangerous, Violent, or Hateful Content | 0001 |
| Confabulation | 0002 |
| Data Privacy | 0003 |
| Information Integrity | 0003, 0006, 0007, 0010 |
| Information Security | 0001, 0002, 0006 |
| Human-AI Configuration | 0005, 0007, 0008, 0009, 0010 |
| Value Chain and Component Integration | 0003, 0004, 0009, 0010 |

reverse-index の粒度は意図的に粗い (function レベル + risk-category
レベル、subcategory レベルではない): NIST の subcategory 番号は RMF
改訂や core RMF と GAI Profile の間で変動するが、AAP 側の判断は
subcategory renumbering で shift しない。NIST が subcategory identifier
を改訂しても、本 file だけが変わり、上記 per-ADR mapping は stable。

## Caveat

NIST AI RMF は living document であり、特に GAI Profile は core RMF
より改訂頻度が高い見込み。上記 mapping は年単位 + 各 profile 改訂
時に再チェックする。mapping shift は ADR 自体には伝播しない。ADR と
本 file は独立した cadence で decay し、ADR は durable な anchor と
して残る。
