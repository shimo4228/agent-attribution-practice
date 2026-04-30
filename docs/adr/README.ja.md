言語: [English](README.md) | 日本語

# Architecture Decision Records

各 ADR は自律 AI エージェントをどう build し動かすべきかについての
構造的判断を記録する — `contemplative-agent` の実装と運用を通じて
発見されたものであり、トップダウンで規定したものではない。

## Index

| ADR | Title | Status |
|-----|-------|--------|
| [0001](0001-security-by-absence.ja.md) | Security by Absence（不在による安全） | accepted |
| [0002](0002-deterministic-prohibition-at-scaffolding.ja.md) | Deterministic Prohibition at the Scaffolding Layer（足場層での決定論的禁止） | accepted |
| [0003](0003-untrusted-content-boundary.ja.md) | Untrusted Content Boundary（信頼しない蓄積内容の境界） | accepted |
| [0004](0004-single-external-adapter.ja.md) | Single External Adapter Per Agent（Agent あたり 1 つの External Adapter） | accepted |
| [0005](0005-human-approval-gate.ja.md) | Human Approval Gate for Behavior-Modifying Commands（行動を変える命令への人間の承認ゲート） | accepted |
| [0006](0006-causal-traceability.ja.md) | Causal Traceability（因果追跡可能性） | accepted |
| [0007](0007-scaffolding-visibility.ja.md) | Scaffolding Visibility（足場の可視性） | accepted |
| [0008](0008-one-agent-one-human.ja.md) | One Agent, One Human (experimental)（1 つのエージェントに 1 人の人間） | experimental |
| [0009](0009-triage-before-autonomy.ja.md) | Triage Before Autonomy (experimental)（自律採用の前に triage する） | experimental |
| [0010](0010-phase-separation.ja.md) | Phase Separation Between Design and Operation (experimental)（設計フェーズと運用フェーズの分離） | experimental |

ADR-0001, 0002, 0003 は **prohibition-strength hierarchy（禁止強度の
階層）** を成す: absence (最強) → scaffolding enforcement → untrusted
boundary (確率的、最弱)。Prohibition を設計する時はこの階層を上から
歩く。

ADR-0009 と 0010 は **triage pair** を成す。ADR-0009 は適用順序で他
ADR の *手前* に位置する: 業務が Autonomous Agentic Loop Quadrant (10
本すべての ADR が load-bearing) にあるか、LLM Workflow Quadrant
(0001–0007 が適用される) にあるか、射程外 (Script / Algorithmic Search
Quadrants) にあるかを triage する。ADR-0010 は Autonomous Agentic
Loop Quadrant の component が deployment の operation phase に配置
された時に *Phase-crossing decision（フェーズ越境判定）* を表に出す。
Phase (design vs operation) は Quadrant と独立 — 各 Quadrant は両 phase
に出現しうる。Navigator は [`../quadrants/`](../quadrants/) を参照。
合わせて 10 本の ADR と 4 つの Business AI Quadrants が repository の
**二軸構造** を成す。

## Format

各 ADR は以下に従う: Status / Date / Context / Decision / Alternatives
Considered / Consequences。意図は、*decision* — 何を制約すべきか、誰が
責任を負うか — が実装変更を超えて persist すること。

[../thesis.ja.md](../thesis.ja.md) で根底の thesis (*accountability
distribution（責任の分配）*、capability distribution ではなく) を、
[../quadrants/](../quadrants/) で adoption navigator を、
[../manifesto.md](../manifesto.md) で本 work が答えようとしない open
question を参照。
