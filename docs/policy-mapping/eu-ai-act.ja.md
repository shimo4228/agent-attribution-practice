言語: [English](eu-ai-act.md) | 日本語

# EU AI Act — AAP ADR へのマッピング

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-06-03
**Framework version:** Regulation (EU) 2024/1689 (Artificial
Intelligence Act)、2024-08-01 発効。執筆時点の適用日程に対して読む:
prohibited practices (Art 5) は 2025-02-02、general-purpose AI
model 義務は 2025-08-02 に適用。high-risk の適用日は、Omnibus
"Digital Package" の暫定政治合意 (2026-05-06、Council 確認
2026-05-13) により延期された: stand-alone な Annex III high-risk
義務は 2026-08-02 から 2 December 2027 へ、Annex I (製品組込)
high-risk 義務は 2027-08-02 から 2 August 2028 へ移動する。これら
延期後の日付は、Omnibus が正式採択され Official Journal に公布
されるまで暫定である。
**Maintenance posture:** This file decays. The ADRs do not.
**Maintenance cadence:** Article の読み方を変える delegated /
implementing act・harmonised standard・Commission guideline が出る
たび、それ以外は年単位。2026–2027 の段階適用期は NIST/ISO より
decay が速い見込み。

> **本記述は AAP の reading である**。以下の per-ADR mapping は、
> AAP の ADR と EU AI Act の 1 つ以上の Article との関係を AAP が
> どう読んでいるかを記録する。compliance attestation ではない: ADR
> の採用が Article の充足を保証する、あるいはその逆を保証する、と
> AAP は主張しない。legal advice ではない。AI Act の authoritative
> interpretation は欧州委員会・AI Office・各国 competent authority、
> および deploying organization の jurisdiction における qualified
> legal / compliance counsel に残る。mapping は cross-reference の
> ための citation surface として提供されるものであって、規制本文
> 自体の代替ではない。

## Purpose

EU AI Act は *risk-tiered な義務レジーム* である。AI システムを 4
段階に仕分け、義務を抽象的な capability ではなく band に紐づける:
**unacceptable risk** (禁止行為、Art 5)、**high risk** (Chapter III
の義務スタック。provider 向け Art 8–15 + deployer 義務 Art 26–27)、
**limited risk** (透明性義務、Art 50)、**minimal risk** (強制義務
なし)。

Act は *obligation* を ship する。AAP は *judgment* ── 何を制約し、
制約をどこに置き、失敗時に誰が帰結を負うか ── を記録する。2 つの
layer は相補的: Act は high-risk な autonomous システムにどの義務が
紐づくかを示し、AAP はそのうち autonomous-agent subset に対する
構造的な答えを記録する。組織はそれを採用して義務を discharge できる。

本 file 全体で使う pattern phrase:

> **AI Act は obligation を ship する。AAP は、その obligation を
> いつ / なぜ / どのコストで構造的に discharge するかの judgment を
> 記録する** ── Act は義務 (human oversight、record-keeping、
> robustness) を述べ、mechanism は provider/deployer に委ねる。AAP
> はその mechanism choice を autonomous-agent subset 向けに populate
> する。

### 2 つの graduated structure についての注記

Act の 4 risk tier と AAP の 3 段階 *prohibition-strength hierarchy*
(ADR-0001 → 0002 → 0003) は、どちらも graduated だが **異なるもの**
を段階づけており、互いに collapse させてはならない:

- Act の tier は *システム全体がどれだけ危険か* (unacceptable →
  minimal) を段階づけ、それが *どの義務が紐づくか* を決める。
- AAP の prohibition-strength hierarchy は *単一 capability が
  どれだけ強く foreclose されているか* (absence → deterministic
  scaffolding gate → untrusted-content boundary) を段階づける。これ
  は 1 つの義務を discharge する *内側* の mechanism-strength 軸。

high-risk システム (Act tier) は、その Art 15 robustness 義務を AAP
hierarchy のどの tier を使っても discharge できる。両者の類比は、
達成可能なら mitigation より elimination を選ぶ点にある (Art 9(2)(a)
は *設計による risk 除去* を mitigation の前に置く。ADR-0001 は
absence を restriction の前に置く)。これは identity ではない。

本 file 外の文脈は
[`../adr/README.ja.md`](../adr/README.ja.md) (ADR index)、
[`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md)
(quadrant ごとの control density)、
[`./README.ja.md`](README.ja.md) (ディレクトリ規約、non-attestation
disclaimer、revision procedure、[`../industry-mapping.ja.md`](../industry-mapping.ja.md)
との sibling 関係) を参照。

## Per-ADR mapping

### ADR-0001 Security by Absence

[ADR-0001](../adr/0001-security-by-absence.ja.md) は、最強の禁止が
「capability をそもそも実装しない」── policy review ではなく `grep`
で答えられる ── ものだと記録する。これは最も直接的に **Art 9
(risk management system)** に対応する。同条 2(a) は *適切な設計・
開発による risk の除去または低減* を mitigation 措置の前に位置づける。
また **Art 15 (accuracy, robustness, cybersecurity)** にも対応し、
absent な capability はこれを構成的に discharge する ── robust に
すべき surface が存在しない。capability の除去がシステムを Annex III
high-risk use case の外へ動かす場合、ADR-0001 は上流の **Art 6 /
Annex III** 分類境界にも触れる。Act は risk を管理し設計で除去する
義務を述べる。AAP は、absence が restriction より構造的に強く、監査
test が review ではなく `grep` で答えられる、という judgment を記録
する。

### ADR-0002 Deterministic Prohibition at the Scaffolding Layer

[ADR-0002](../adr/0002-deterministic-prohibition-at-scaffolding.ja.md)
は、absence が達成できない場合の次に強い禁止が、scaffolding layer
での deterministic enforcement (PreToolUse hook、構造的 quarantine、
adapter gate) であり、LLM の外側で一致入力の 100% に発火することだ
と記録する。これは **Art 15** に対応する。その robustness /
cybersecurity 義務 (system vulnerability の exploitation を通じて
利用や挙動を変えようとする試みへの resilience を求める Art 15(5) を
含む) は、probabilistic gate より deterministic gate の方がよく満た
される。また **Art 14(3)** にも対応する。同項は、deployer に実装を
委ねる措置とは別に、provider が high-risk システムに *組み込む*
oversight 措置を想定している。Act は lifecycle を通じた一貫した
performance と resilience を求める。AAP は、prompt-clause /
convention-file / constitutional enforcement が通常条件で 50–80%、
adversarial 下ではそれ以下しか comply せず、構造的に誠実な discharge
は non-LLM gate だ、という judgment を記録する。

### ADR-0003 Untrusted Content Boundary

[ADR-0003](../adr/0003-untrusted-content-boundary.ja.md) は、蓄積
された agent state すべて ── episode log、knowledge store、distilled
identity、agent 自身の過去出力 ── を、prompt に読み戻す際は untrusted
content として扱う、と記録する。これは **Art 15(5)** に対応する。
同項は *data poisoning、model poisoning、adversarial examples、
confidentiality attacks* への resilience を cybersecurity 義務として
挙げる。また **Art 10 (data and data governance)** にも対応し、その
品質・provenance 要件はシステムが自分自身に feed back する data にも
及ぶ。Act は入力および training/運用 data の manipulation への
resilience を求める。AAP は、trust boundary が agent 自身を含まねば
ならない (agent 自身の distillation 出力は untrusted な upstream
source から taint を継承する) ── load 時に fail-closed validation、
injection 時に明示的な `<untrusted_content>` marker ── という
judgment を記録する。

### ADR-0004 Single External Adapter per Agent Process

[ADR-0004](../adr/0004-single-external-adapter.ja.md) は、各 agent
process が高々 1 つの external side-effect adapter を持ち、blast
radius が事後観測ではなく設計で bound される、と記録する。これは
**Art 9 (risk management)** に対応する (impact surface の設計時
bounding)。また **Art 12 (record-keeping)** にも対応する。単一の
side-effect surface は、絡み合った multi-adapter trace ではなく
単一で attributable な log stream を生む。さらに **Art 14 (human
oversight)** にも対応する。bound された surface は監視者が実際に
monitor できる。Act は provider に risk-management 措置の選択を許す。
AAP は、bounding-at-design が redirect を保つ (各々 1 adapter・各々
1 accountability chain を持つ複数 agent) のに対し、observe-at-runtime
はどの adapter がどの side effect を起こしたかの事後 untangling を
要する、という judgment を記録する。

### ADR-0005 Human Approval Gate

[ADR-0005](../adr/0005-human-approval-gate.ja.md) は、agent の
skill / rule / identity / constitution を変更する全コマンドが、書込
前に named human の明示的承認を要し、`--auto` flag を持たない、と
記録する。これは **Art 14 (human oversight)** への load-bearing な
AAP mapping である ── とりわけ oversight が可能にせねばならない
Art 14(4) の能力: 出力を *正しく解釈* する (14(4)(c))、それを
*使わない・無視・override・reverse する決定* (14(4)(d))、*"stop"
button 類似の手続で intervene または interrupt する* (14(4)(e))。
synchronous な pre-write gate は、stop button の agent-state 版だ:
behaviour-modifying な書込は human が動くまで起きない。Act は
oversight が *effective* であることを求める。AAP は、gate が
synchronous かつ pre-write でなければならず (advisory confirmation
と preview-then-run は stochastic generation 下で失敗する)、second
LLM ではなく human で終端せねばならない ── probabilistic gate は
prompt injection 下で fail open し、それは Art 14 oversight を
effective ではなく nominal にしてしまう ── という judgment を記録
する。

### ADR-0006 Causal Traceability

[ADR-0006](../adr/0006-causal-traceability.ja.md) は、incident 後に
operator が、どの入力が agent に届き、どの scaffolding version が
active で、どの承認がその version を gate し、どの external surface
が触れられ、最終出力が何だったかを再構成できる、と記録する。これは
**Art 12 (record-keeping)** に強く対応する。同条は high-risk
システムに *lifetime にわたり event (log) を自動記録* することを求め
る。また **Art 19** (provider にその自動生成 log の保持を求める) と
**Art 13 (transparency and provision of information to deployers)**
(システムの動作が報告できるほど legible であることを前提とする) にも
対応する。Act は logging 義務を述べる。AAP は、再構成可能な形が
build-time structural (append-only log、version-pin された
scaffolding、ADR-0005 gate からの audit trail) でなければならない ──
runtime observability (telemetry、metric、alert) は necessary だが
autonomous-agent の再構成には insufficient だから ── という judgment
を記録する。

### ADR-0007 Scaffolding Visibility

[ADR-0007](../adr/0007-scaffolding-visibility.ja.md) は、agent の
非 weights component すべて ── system prompt、persona、rule、
identity、tool 定義、RAG index、agent loop、safety gate、runtime
harness ── が scaffolding であり、version control 下の file として
materialize され、ADR-0005 gate を通じてのみ変更可能だ、と記録する。
これは **Art 11 / Annex IV (technical documentation)** に対応する
(システムの設計・component・control logic の記述を要する)。また
**Art 13 (transparency)** と **Art 14(4)(a)** (監視者がシステムの
*relevant capacities and limitations を理解できる* ことを要する) に
も対応する。inspectable な file に住む behaviour は documentable で
understandable だが、weights に internalize された behaviour はそう
でない。Act は technical documentation を求める。AAP は、documentation
だけでは insufficient ── behaviour は file の *記述* ではなく file に
*住まねば* ならない、なぜなら weights-internalized な behaviour
(RLHF / Constitutional AI / instruction tuning 経由) は accountable
で documentable な layer から完全に出てしまうから ── という judgment
を記録する。

### ADR-0008 One Agent, One Human (experimental)

[ADR-0008](../adr/0008-one-agent-one-human.ja.md) は、各 agent
process が deployment 時に、accountable operator たる唯一の
identifiable human に束縛される、と記録する。これは **Art 14**
(human oversight を *natural persons によって* 行うものとして枠づ
ける) と **Art 26(2)** (deployer に、*必要な competence・training・
authority、および必要な support を持つ natural persons へ human
oversight を割り当てる* ことを求める) に対応する。Act は named
natural person が oversight role を担うことを求める。AAP は、chain
の終端が agent process ごとに 1 人の named human でなければならず
(rotation は formal で明示的に logged な handoff があれば compatible)、
team や共有 role ではいけない ── autonomous agent 下の共有
accountability は moral-crumple-zone failure を再現するから ── という
judgment を記録する。Status: **experimental** ── binding pattern は
構造的に誠実だが、多様な組織設定での validation はまだ。

### ADR-0009 Triage Before Autonomy (experimental)

[ADR-0009](../adr/0009-triage-before-autonomy.ja.md) は、autonomous-
loop architecture を採る前に、その work を 4 Business AI Quadrants
で triage し、autonomous loop は Autonomous Agentic Loop Quadrant
に予約され、それを選ぶことは deploying organization が attribution
gap を pre-deployment cost として、identifiable な gap-bearer を
named することで吸収する commitment になる、と記録する。これは
**Art 6 / Annex III** の分類ステップ (ある利用が high-risk かを
決めること自体が最初の triage) に、**Art 9** (risk-management system
は deployment の前後と全体にわたり *確立・実装・文書化・維持* され、
事後発見ではない) に、そして **Art 27 (fundamental rights impact
assessment)** (一部 high-risk 利用に対する deployer 側の pre-deployment
assessment) に対応する。Act は pre-deployment の分類と risk-assessment
の姿勢を求める。AAP は、autonomous loop の deployment 判断が構造的な
attribution gap を負い、その gap が deployment 時に named subject に
よって負われねばならない、という judgment を記録する。Act の scholarly
reading は収束する結論 ── *untraceable な behavioural drift を持つ
high-risk agentic システムは、現状 Act の essential requirements を
満たせない* (Nannini et al., arXiv:2604.04604) ── に達している。
triage が存在するのは、Autonomous Agentic Loop Quadrant では、その
gap が incidental ではなく構造的だからだ。Status: **experimental**。

### ADR-0010 Phase Separation Between Design and Operation (experimental)

[ADR-0010](../adr/0010-phase-separation.ja.md) は、Autonomous
Agentic Loop Quadrant component の operation-phase 配置が、ADR-0009
の gap-bearer naming に加えて明示的な *Phase-crossing decision* を
要する ── operation で新 pattern が surface したとき、autonomous
loop がその場で動的に handle するのか、feedback として design に
戻すのか? 両答とも admissible、「その時考える」は不可 ── と記録する。
これは **Art 9** (risk management を *entire lifecycle にわたる
continuous iterative process* として枠づける) に、**Art 17 (quality
management system)** (design と運用 governance に跨る) に、そして
**Art 72 (post-market monitoring)** (Act の明示的な operation-phase
feedback 義務 ── deployed システムの emergent behaviour が provider
へ流れ戻る channel) に対応する。Act は design と operation を、最適化
軸が反転した phase としては名指さない。AAP は、その区別が autonomous
loop にとって load-bearing であり、配置が deferral ではなく recorded
な deployment-time decision を要する、という judgment を記録する。
Status: **experimental**。

## Per-Quadrant note

AAP の 4 Business AI Quadrants は problem-space カテゴリであり、agent
カテゴリではない。Act はこれらの軸で AI work を分類しない ── risk
tier と use case で分類する。だが AAP の quadrant-to-ADR matrix
([`../quadrants/governance-mapping.ja.md`](../quadrants/governance-mapping.ja.md))
は Act の high-risk 義務スタックにきれいに対応する:

- **Quadrant 1 (Script)** と **Quadrant 2 (Algorithmic Search)** ──
  AAP scope 外。Act 上 high-risk かは use case 次第でこれら quadrant
  には依らず、本 file から mapping は提供しない。
- **Quadrant 3 (LLM Workflow)** ── 6 ADRs が適用 (0001, 0003, 0004,
  0005, 0006, 0007)。そうした workflow が Annex III high-risk 利用で
  deploy される場合、これらは Art 9–15 provider stack と Art 14
  oversight 義務を *medium* control density で populate する。
- **Quadrant 4 (Autonomous Agentic Loop)** ── 10 ADRs すべて適用。
  ここでは ADR-0008, ADR-0009, ADR-0010 が load-bearing で、とりわけ
  **Art 14** (named natural person による oversight)、**Art 9**
  (lifecycle risk management)、**Art 72** (post-market monitoring) に
  強く効く。同一の Article 14 義務が、Quadrant 3 (role-named LLM
  call、deterministic control flow) と Quadrant 4 (pre-named
  gap-bearer + Phase-crossing decision) では全く異なる AAP 側
  instantiation を受ける。

## Reverse index — EU AI Act Article → 該当 ADR

| EU AI Act Article (主題) | 該当 AAP ADR |
|---|---|
| **Art 5** — 禁止行為 (unacceptable risk) | *(tier 境界。禁止を discharge すると主張する ADR はない ──「2 つの graduated structure」参照)* |
| **Art 6 / Annex III** — high-risk 分類 | 0001, 0009 |
| **Art 9** — risk management system | 0001, 0004, 0009, 0010 |
| **Art 10** — data and data governance | 0003 |
| **Art 11 / Annex IV** — technical documentation | 0007 |
| **Art 12** — record-keeping (logging) | 0004, 0006 |
| **Art 13** — deployer への transparency | 0006, 0007 |
| **Art 14** — human oversight | 0002, 0004, 0005, 0007, 0008 |
| **Art 15** — accuracy, robustness, cybersecurity | 0001, 0002, 0003 |
| **Art 17** — quality management system | 0010 |
| **Art 19** — 自動生成 log の保持 | 0006 |
| **Art 26** — deployer 義務 (26(2) named overseer 含む) | 0008 |
| **Art 27** — fundamental rights impact assessment | 0009 |
| **Art 72** — post-market monitoring | 0010 |

reverse-index の粒度は意図的に粗い (Article level、Act の wording が
load-bearing な箇所のみ named paragraph ── 例 Art 9(2)(a)、
Art 14(4)(e)、Art 15(5)、Art 26(2))。delegated act・implementing
act・harmonised standard は各 Article の読み方を refine するが
Article 自体を renumber しない。それらが出ても本 file だけが変わり、
上の per-ADR mapping は安定する。

## Secondary scholarship and external convergence

Act には急成長する secondary literature があるが、本 file はその
review ではない。2026 年の 3 source を挙げるのは、それらが AAP が
実装から記録した judgment に独立して到達した ── または mechanism を
供給する ── という理由だけからである。これらは AAP ADR lineage の
一部 *ではない* (ADR は `contemplative-agent` 運用から抽出されたもの
で、これら paper からではない)。non-attestation disclaimer の精神で、
external citation surface として列挙する。

- **Nannini et al., *AI Agents Under EU Law* (arXiv:2604.04604,
  2026-04).** agentic システムを Act にマップする legal-scholarship。
  *untraceable な behavioural drift を持つ high-risk agentic
  システムは現状 Act の essential requirements を満たせない* という
  結論は、**ADR-0006** (causal traceability) と **ADR-0009** (triage
  before autonomy) に収束する: いずれも untraceability を、調整可能な
  欠陥ではなく disqualifying なものとして扱う。
- **Safin & Balta, *Autonomy and Agency in Agentic AI:
  Architectural Tactics for Regulated Contexts* (arXiv:2605.12105,
  2026-05).** oversight 義務を discharge するための architectural
  tactics ── うち *checkpoints* と *write staging* (不可逆な action
  を、commit 前に human が確認する可逆な proposal へ変換する) ── を
  提案する。これらは **ADR-0005** を Art 14 の下で instantiate する
  のに組織が使える *mechanism* である。本 repository の thesis
  (*implementation dissolves; judgment persists*) と整合的に、tactic
  は ADR 本文ではなくこの mechanism-mapping layer に属する: ADR は
  judgment (human が behaviour-modifying な書込を着地前に承認する) を
  記録し、write-staging はその 1 つの shipping 実装であって、実装は
  decay する。
- **Bhattarai & Vu, *Trustworthy Agentic AI Requires Deterministic
  Architectural Boundaries* (arXiv:2602.09947, 2026-02).**
  *probabilistic に学習された behaviour ではなく deterministic で
  architectural な enforcement* が trustworthy agentic AI に necessary
  だと論じ、untrusted input + privileged data access + external
  action capability の "lethal trifecta" を名指す。これは独立に運用
  から到達した **ADR-0002** (deterministic prohibition at the
  scaffolding layer) と **ADR-0003** (untrusted-content boundary) に
  収束する。

## Caveat

EU AI Act は 2026–2027 にかけて active な段階適用期にあり、delegated
act・implementing act・Commission guideline・harmonised standard が
なお到着しつつある。Article の wording は stable だが、*各 Article を
どう読むか* はまだ settle していない。本 mapping はそうした instrument
ごと、かつ少なくとも年単位で再確認する。mapping の変動は ADR 自体に
伝播しない。ADR と本 file は独立した cadence で decay する; ADR が
durable な anchor である。
