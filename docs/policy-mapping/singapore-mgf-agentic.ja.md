言語: [English](singapore-mgf-agentic.md) | 日本語

# Singapore Model AI Governance Framework for Agentic AI — AAP ADR へのマッピング

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-06-12
**Framework version:** IMDA, *Model AI Governance Framework for
Agentic AI*。2026-01-22 に初版公開 (World Economic Forum 2026 で
発表)、agentic AI に特化した初の国家 governance framework。本
mapping は **Version 1.5 (2026-05-20 公開、2026-06-05 更新)** に
対して書かれている。v1.5 は v1.0 以降 60 社超からの feedback を
反映済みで、launch 版には無かった *systemic and multi-agent risks*
節 (1.2.3) を含む。
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** MGF の version ごと (IMDA は framework を
*living document* として *継続的に更新する* と明言しており、v1.0 →
v1.5 の間隔は 4 ヶ月未満。本 directory 中で最速の decay を見込む)。
それ以外は年単位。

> **本記述は AAP の reading である**。以下の per-ADR mapping は、
> AAP の ADR と MGF for Agentic AI の 1 つ以上の section との関係を
> AAP がどう読んでいるかを記録する。compliance attestation ではない:
> ADR の採用が MGF practice の充足を保証する、あるいはその逆を保証
> する、と AAP は主張しない。legal advice ではない。framework の
> authoritative interpretation は IMDA、および deploying
> organization の jurisdiction における qualified legal / compliance
> counsel に残る。mapping は cross-reference のための citation
> surface として提供されるものであって、framework 本文自体の代替
> ではない。

## Purpose

MGF for Agentic AI は *voluntary な best-practice catalogue* で
あって obligation regime ではない: 準拠は任意である一方、agent の
behaviour に対する法的 accountability は準拠と無関係に deploying
organization に残る。**(1) assess and bound the risks upfront**
(§2.1)、**(2) make humans meaningfully accountable** (§2.2)、
**(3) implement technical controls and processes** (§2.3)、
**(4) enable end-user responsibility** (§2.4) の 4 dimension で
構成され、明示的に iterative process として framing される:
monitoring で anomaly を検出したら、組織は再 assess・再 bound へ
戻る。

本 directory で map する framework のうち、MGF は AAP の主題に最も
近い: autonomous agent の *ために特化して* 書かれた唯一の framework
であり、AAP の core problem を自身の言葉で名指している ──
*"Multiple stakeholders may also be involved in different parts of
the agent lifecycle, diffusing accountability"* (§2.2)。それゆえ
関係の密度は NIST / ISO / AI Act より高く、緊張もまた高い (後述
*Convergence and tension* 参照)。

本 file を通して使う pattern phrase:

> **MGF は practice catalogue を ship する。AAP は、どの practice
> がいつ・なぜ・どのコストで構造的に十分かという judgment を記録
> する** ── MGF は emerging best practices を列挙して選択を組織に
> 委ね、AAP はどの選択が attribution にとって load-bearing で、
> どれが autonomy の下で fail するかを記録する。

本 file 外の文脈は
[`../adr/README.md`](../adr/README.md) (ADR index)、
[`../quadrants/governance-mapping.md`](../quadrants/governance-mapping.md)
(per-quadrant control density)、[`./README.md`](README.md)
(directory レベルの規約、non-attestation disclaimer、revision
procedure) を参照。

## Per-ADR mapping

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.ja.md) は、最強の禁止は
capability が一度も実装されないことだと記録する。これは **§2.1.2
(agent limits)** ── その一般則 *deterministic な limit を
non-deterministic な limit より優先し、design で bound せよ*、例
えば agent に tool を使うなと prompt するのではなく *tool が
そもそも呼べないように access control を課す* ── と、framework の
冒頭で *すべての use case が agent に適するわけではない* と述べる
**§2.1.1** に map する。同じ elimination-over-mitigation の順序
である。MGF は least-privilege bounding を practice として ship
する; AAP は bounding の上にもう 1 段あるという judgment ──
存在しない tool は un-prevent できない、その audit test は
configuration review ではなく `grep` で答えられる ── を記録する。

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.ja.md)
は、absence が達成できない場合の次に強い禁止は、LLM の外で
matching input の 100% に発火する scaffolding 層の deterministic
enforcement だと記録する。これは本 file 中最強の収束である:
**§2.3.1** は control 選択時に *structural, rule-based* を
*model-based or prompt-layer* より優先せよと指示し ── *"agent に
特定 tool を使うなと指示するのではなく、tool 層で access control
を実装できる"* ── prompt-layer safeguard は *ユーザー間で一貫して
定義されない傾向がある一方、system-level safeguard は一貫して定義
し enforce できる* と注記する。ADR-0002 が記録するのと同じ
probabilistic vs deterministic の区別に、単一 agent の運用では
なく deployment practice の側から到達している。MGF は *固定 rule
で定義しにくい* risk のために model-based safeguard を残す;
ADR-0002 の scope はより狭い ── 部分的準拠が failure である
*禁止* ── がゆえに、probabilistic な tier を一切認めない。

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.ja.md) は、蓄積
された agent 状態のすべて ── agent 自身の過去の出力を含む ── を、
prompt へ読み戻す際に untrusted content として扱うと記録する。
これは、common threat に *memory poisoning* を挙げ、複雑な agentic
システムには *taint tracing* ── *untrusted data がシステム内を
どう動きうるか* を map する ── を推奨する **§2.1.1 の threat-
modelling 注記**と、agent 間は free text ではなく structured
schema で通信させ shared memory access を制限せよという **§2.3.1
(multi-agent interactions)** に map する。MGF は taint をシステム
内で trace すべきものとして扱う; AAP はさらに進んだ judgment ──
trace は agent 自身で終端する。distillation 出力は untrusted な
上流から taint を継承するので、蓄積状態が trusted として loop に
再入する地点は存在しない ── を記録する。

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.ja.md) は、各 agent
process が持つ external side-effect adapter は最大 1 つで、blast
radius が design で bound されると記録する。これは **§2.1.2
(agent limits)** ── least-privilege な tool access、*natural
constraint* としての *functional boundary による agent 構造化*
── と、framework 自身の worked example (§2、IMDA case study) ──
*無制限 access を持つ単一の「all-powerful」agent* を避け *狭く
明確に定義された rule を持つ複数 agent* を使え ── に map する。
MGF は分割を risk bounding で動機づける; AAP は attribution 上の
動機を追加で記録する: process あたり 1 adapter は accountability
chain あたり 1 本の帰属可能な side-effect stream を生むが、
multi-adapter agent ではどの surface がどの effect を生んだかの
事後の解きほぐしが要る。

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.ja.md) は、agent の
skills・rules・identity・constitution を変更するすべての command
に、名指された human による同期的な pre-write 承認を要すると記録
する。これは **§2.2.2 (design for meaningful human oversight)**
に map する: high-stakes / 不可逆な action の前に human approval
を要する *significant checkpoints or action boundaries* を定義
せよ; approval request は contextual で digestible に保て; そして
── fail-closed 条項 ── *approval infrastructure が fail したら
default で action を deny せよ*。§2.2.2 は ADR-0005 が前提とする
audit posture も供給する: rubber-stamping を検出するために human
override rate と response time を監視せよ ── 常に承認される
approval gate は名ばかりの oversight だからである。MGF は
checkpoint catalogue を ship する; AAP は *behaviour-modifying な
書込* に限っては gate が同期的・pre-write で、第二の LLM ではなく
human で終端しなければならないという judgment を記録する ──
probabilistic な承認者は prompt injection の下で fail open する。

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.ja.md) は、incident 後
に operator が、どの input が agent に到達し、どの scaffolding
version が active で、どの承認がその version を gate し、どの
external surface に触れ、最終出力が何だったかを再構成できると
記録する。これは **§2.1.2 (agent identity)** ── agent identity は
unique で、*accounted for* (accountable な主体に紐づく)、*行為
する capacity に応じて differentiated* かつ *auditability のため
に record* され、権限の委譲は *clearly recorded* される ── と、
**§2.3.3 (continuous testing and monitoring)** ── failure 地点の
特定のため *agent workflow の各 step と agent 間 interaction* を
log・trace し、問題ある trajectory が削除できないよう *log
immutability を確保* せよ ── に map する。MGF は identity と
logging の practice を ship する; AAP は再構成が *build-time
structural* (append-only log、version-pinned scaffolding、承認
audit trail) でなければならないという judgment を記録する ──
runtime observability だけでは *どの scaffolding version が、
誰の承認の下で* という事後の問いに答えられない。

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.ja.md) は、agent の
非 weights 構成要素のすべてが scaffolding であり、version 管理
された file として実体化され、ADR-0005 の gate を通じてのみ変更
されると記録する。MGF に直接の対応物はない ── 最も近い practice
は、変更 (model update・tool 変更・autonomy 調整) に定義済み
trigger と risk 分類された review を求める **§2.3.3 (robust
change management)** と、agent を名前付き component (instructions・
tools・controls・logging) に分解する §1.1.1 である。MGF が要求
*しない* のは、behaviour が *inspectable で versioned な file に
住む* ことそのものである; 行動制約が vendor 側 configuration に
しか存在しない deployment は、MGF の change-management practice
を満たしつつ ADR-0007 を fail しうる。AAP はこれを conflict では
なく framework が open に残した judgment として記録する: MGF は
*変更をどう review するか* を catalogue し、ADR-0007 は *変更
される物がどこに住むことを許されるか* を制約する。

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.ja.md) は、各 agent
process が deployment 時に、ちょうど 1 人の identifiable な human
accountable operator に束縛されると記録する。これは **§2.2.1
(clear allocation of responsibilities)** と、*agent に責任を持つ
human contact point* をユーザーに提供せよという **§2.4.2** に
map する。ただしこの mapping は明示的な divergence を伴う: §2.1.2
は agent identity が *supervising agent・human user・organisational
department のいずれか* に紐づくことを許す。ADR-0008 は chain の
終端が agent process あたり 1 人の名指された human でなければ
ならないという judgment を記録する ── department 終端は、moral-
crumple-zone failure が運用上最も近い者に着地する shared-
accountability 構成を再生産し、supervising-agent 終端は問いを
1 hop 先送りするだけで答えない。Status: **experimental** ── そして
この divergence こそ、experimental status が待っている組織多様性
の問いそのものである。

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.ja.md) は、
autonomous-loop architecture の採用前に作業を 4 つの Business AI
Quadrant で triage し、Autonomous Agentic Loop Quadrant の選択は
deployment 時の gap-bearer 名指しを組織に commit させると記録
する。これは **§2.1.1 (determine suitable use cases)** ── *すべて
の use case が agent に適するわけではなく、deterministic workflow
の方がよく仕える場合もある*。その likelihood factor (autonomy の
レベル・task complexity・system complexity) はまさに AAP の triage
入力である ── と、§2.1.2 の締めの practice *evaluating the
residual risks* ── 組織は *agentic deployment の residual risk が
許容可能な水準にあり受容できるかを判定* せよ ── に map する。
収束は ADR の一歩手前で止まる: MGF は residual risk の *受容* を
求めるが、それを *負う* identifiable な主体の名指しまでは求めない。
ADR-0009 は、Autonomous Agentic Loop Quadrant において residual は
量ではなく attribution gap であり、名指された bearer なき受容は
moral-crumple-zone 構成だと記録する。Status: **experimental**。

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.ja.md) は、Autonomous
Agentic Loop Quadrant component の operation-phase 配置には明示的
な Phase-crossing decision ── 運用中に新しい pattern が現れたとき、
loop がその場で動的に処理するのか、design へ feedback として戻す
のか ── を要すると記録する。MGF の lifecycle 背骨はよく map する:
**§2.3.1 / §2.3.2 / §2.3.3** は design-time control・pre-deployment
testing・post-deployment monitoring を分離し、§2 は 4 dimension を
*iterative process* として framing する ── monitoring で検出した
anomaly は組織を再 assess・再 bound へ戻す。この矢印は route-back-
to-design の辺 *そのもの* である。さらに §2.3.3 の *robust change
management* は review 再入の定義済み trigger を求める。MGF は
feedback loop を practice として ship する; AAP は autonomous loop
については *その場で動的処理* と *design へ戻す* の選択を
deployment 時に明示的に行わなければならないという judgment を
記録する ── 「起きたときに考える」は Phase-crossing decision を
autonomous loop 自身に委譲することであり、それは同じ gap を継承
する。Status: **experimental**。

## Per-Quadrant note

MGF は agentic な作業を AAP の quadrant 軸では分類せず、risk
factor (§2.1.1) で practice をスケールさせる。2 つの grading は
競合せず compose する:

- **Quadrant 3 (LLM Workflow)** ── MGF の risk-factor 表は
  *quadrant 境界をすでに価格付けしている*: 低 autonomy (「agent に
  SOP が与えられる」) と低 system complexity (「sequential workflow
  を実行する単一 agent」) は §2.1.1 の low-likelihood 端に座る。
  適用される 6 ADR (0001, 0003, 0004, 0005, 0006, 0007) が §2.1–2.3
  の practice を medium control density で populate する。
- **Quadrant 4 (Autonomous Agentic Loop)** ── 同じ factor の高い側
  (*自身の judgment を使う* agent、multi-agent interaction、§1.2.3
  の emergent behaviour)。10 ADR すべてが適用され、ADR-0008 / 0009 /
  0010 がここで load-bearing になる ── それぞれ §2.2 (human
  accountability)、§2.1.1 (use-case triage と residual risk)、
  §2.3.3 (post-deployment monitoring と change management) に対して。

## Convergence and tension — value chain と gap-bearer

accountability の拡散に対する MGF §2.2 の回答は *chain* である:
agentic value chain (model developers・tooling providers・platform
providers・system providers / app developers・deployer・end users
── §2.2.1、v1.5 で精緻化) を横断し、内部 team を横断し、外部との
契約を横断して責任を配分する。framework は、自身が扱う問題が
*diffusion* ── lifecycle の異なる部分に複数の actor が関与する
こと ── だと率直に認めている。

AAP の reading: chain は *work* を配分する構造としては正しく、
§2.2.1 の契約的 practice (義務配分の明確化、third-party opacity
への対処) は実在する mechanism である。しかし chain は配分する
のであって、終端しない。追加されるどの link も潜在的な転嫁点で
あり、関与度の曖昧な multi-link chain の下では、事後の attribution
は無限に循環しうる ── 各 link が次の link を指す ── どこかの link
が、循環の止まる場所として *事前に* 指定されない限り。その終端
指定こそ、[ADR-0008](../adr/0008-one-agent-one-human.ja.md) (agent
process あたり 1 人の名指された human) と
[ADR-0009](../adr/0009-triage-before-autonomy.ja.md) (autonomy の
代価としての名指された gap-bearer) が MGF の chain に追加するもの
である。2 つの layer は両立する ── chain は配分可能な部分を配分し、
名指された bearer は配分されない部分を吸収する ── が、同じ主張
ではない。そして終端指定なしに chain だけを採用すれば、moral-
crumple-zone 構成は到達可能なまま残る。

## Reverse index — MGF section → 該当 ADR

| MGF for Agentic AI section (主題) | 該当 AAP ADR |
|---|---|
| **§1.2.3** — systemic and multi-agent risks | 0003, 0004, 0009 |
| **§2.1.1** — use case 適合判定; risk factors; threat modelling | 0001, 0003, 0009 |
| **§2.1.2** — agent limits (deterministic bounding, least privilege) | 0001, 0002, 0004 |
| **§2.1.2** — agent identity (unique, accounted-for, 委譲の記録) | 0006, 0008 |
| **§2.1.2** — evaluating the residual risks | 0009 |
| **§2.2.1** — 責任配分 (value chain, 契約) | 0008 |
| **§2.2.2** — meaningful human oversight (checkpoints, 承認 audit, fail-closed default) | 0005 |
| **§2.3.1** — design-time technical controls (structural vs prompt-layer) | 0002, 0003 |
| **§2.3.2** — pre-deployment testing (multi-agent レベルの testing を含む) | 0010 |
| **§2.3.3** — continuous monitoring; log immutability; change management | 0006, 0007, 0010 |
| **§2.4.2 / §2.4.3** — end-user transparency; human contact points | 0008 |

## Caveat

MGF for Agentic AI は明示的に living document である: v1.5 は v1.0
から 4 ヶ月未満で到着し、本 file が引く題材 (value chain、multi-
agent risks) を再構成した。section 番号と practice の文言は動く
ことを見込むべきである。本 mapping は MGF の version ごと、かつ
少なくとも年単位で再確認する。mapping の変動は ADR 自体に伝播
しない。ADR と本 file は独立した cadence で decay する; ADR が
durable な anchor である。
