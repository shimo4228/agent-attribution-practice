言語: [English](thesis.md) | 日本語

# Thesis — Accountability Distribution（責任の分配）

> このプロジェクトの名前は **Agent Attribution Practice (AAP)**。名前は
> *practice* を指し、本 thesis は practice が達成するものを指す。
> Attribution — 誰が振る舞いを書いたか、誰が原因か、誰が事後に辿れる
> か — が分配される対象。Accountability は practice が hold した時に
> attribution が取る downstream の形。

## この repository が何か

自律 AI エージェントの実装・運用を通じて発見された、agent-equipped
system 全体で accountability（責任）をどう分配すべきかに関する recurring
な判断の記録。

Governance framework ではない。Policy 集ではない。各判断が Architecture
Decision Record の形を取り、各々が具体実装に grounded されており、
各々が powerful actor を抑える 300 年以上の文明的実践から継承した、
判断の軌跡。

## Core observation

[accountability-wall essay](https://github.com/shimo4228/zenn-content/blob/main/articles/ai-agent-accountability-wall.md)
からよく引用される一節:

> Implementation dissolves; judgment persists. Hooks は別の mechanism
> に置き換わる。看板や物理的障壁は一時的な形。残るのは judgment
> 層 — **何を制約すべきか、誰が責任を負うか**。

そしてその構造的な姉妹命題:

> 組織が数百年かけて磨いてきたのは *capability（能力）* の分配
> ではなく、**accountability（責任）** の分配だ。

この 2 つが本 repository の thesis。続く ADR 群は「何を制約すべきか、
誰が責任を負うか」の partial answer であり、governance theory から
top-down で演繹したものではなく、production で agent を動かす摩擦を
通じて発見されたもの。

## なぜ今この observation が重要か

業界で支配的な AI agent alignment アプローチは、構造的制約を確率的層
に溶かしていくこと: refusal を training で重みに、Constitutional AI
条項を RLHF run に、system prompt を post-training に。各動きは
deterministic な制約 (grep で監査可能、reversible、portable) を
probabilistic な振る舞い (監査不能、retraining 以外で reversible でない、
model 依存) に交換する。

これ自体が悪いわけではない。重要なものを保つ dissolution もある —
inspectability（可検査性）、reversibility、人間の agency の所在。
それを保たなかったり壊したりする dissolution もある。本 repository の
ADR 群は保つ側の dissolution を述べる。

## 10 の原則は何に答えるか

各 ADR は「何を制約すべきか、誰が責任を負うか」のひとつの version に
答える:

| ADR | 制約 | 責任 |
|-----|------|------|
| 0001 Security by Absence | 存在しない capability は呼び出せない | Capability 追加は code review で捕捉される |
| 0002 Deterministic Prohibition at Scaffolding | Absence が unachievable なら weights ではなく harness で禁止する | Hook / quarantine は version 管理されて auditable |
| 0003 Untrusted Content Boundary | 蓄積メモリは authority を grant しない | Operator が tainted ファイルを inspect |
| 0004 Single External Adapter | Blast radius を design で限定 | Adapter ごとに per-process accountability |
| 0005 Human Approval Gate | 行動変更は人間の sign-off を要求 | Audit record の named approver |
| 0006 Causal Traceability | あらゆる event が post-incident に再構成可能 | Operator が audit chain を所有 |
| 0007 Scaffolding Visibility | 振る舞いは weights ではなくファイルに存在 | Operator が動かしているものを inspect できる |
| 0008 One Agent, One Human | Agent process あたり 1 人の named human | Accountability chain の終端 |
| 0009 Triage Before Autonomy *(experimental)* | Autonomous-loop アーキテクチャの採用は除去できない attribution gap（寄与の事後分離不能ギャップ）を引き受ける commitment になる | Triage decision と named gap-bearer（事前命名された責任引受者）が deployment 時に記録される |
| 0010 Phase Separation *(experimental)* | Autonomous Agentic Loop Quadrant の operation phase 配置は ADR-0009 の design-time commitment 単独でカバーできない cost を持つ | Phase-crossing decision（フェーズ越境判定）が deployment 時に明示記録される |

ADRs 0001, 0002, 0003 が
[**prohibition-strength hierarchy（禁止強度の階層）**](glossary.ja.md#prohibition-strength-hierarchy禁止強度の階層)
を成す: absence > scaffolding enforcement > untrusted boundary。
公開されている AI safety 研究の多くは最弱層 (probabilistic な text
制約) に住んでおり、より強い層が存在し利用可能であることを認識して
いない。

Distribution は redirect される subject を要求する。アーキテクチャが
runtime で判断をブレンドする形を選ぶと — **Autonomous Agentic Loop
Quadrant** に本質的に内在する性質 — redirection の宛先は溶けてしまい、
distribution は *prior* な問いになる: そもそも autonomy が要るのか?
ADR-0009 がその prior triage を formalize し、業務を accountability
distribution が保たれるアーキテクチャに振り分ける 4 つの **Business AI
Quadrants（業務 AI 四象限）** (Script、Algorithmic Search、LLM
Workflow、Autonomous Agentic Loop) と pair にする。ADR-0010 が triage
に時間軸を加える: Quadrant 4 の部品が deployment の operation phase
に置かれる時、Phase-crossing decision (operation で現れた新パターンを
in place で handle するか design に feedback として戻すか) が明示
記録される。Phase と Quadrant は独立な dimension のまま; rule は
procedural であって architectural ではない。10 本の ADR と 4 つの
Quadrants が **二軸構造** を成す: ADRs は問いに答え、Quadrants は
業務をその答えがあてはまる場所に振り分ける。Navigator は
[`quadrants/`](quadrants/) を参照。

## 社会的帰結の corollary

上記の判断は内部統制として枠づけられている — 何を制約するか、誰が
答えるか。companion layer は同じ判断を *外側から* 読む — AI が外部化
する責任が、制度（訴訟・補償・規制）へ流れるか、最も見える単一の個人へ
暴力的に収束するかを決める仕組みとして。この読みでは、アカウンタビリ
ティの分配はガバナンスであるだけではない。暴力防止の仕組みである。これは
規範的拡張で、ADR より検証が難しく、上位根拠として
[`social-consequence.ja.md`](social-consequence.ja.md) に分離して置く。

## この repository が主張しないこと

- これら 10 本が完全である。これは 1 つの実装から surface した分。
  他の実装は他のものを surface するだろう。
- 具体実装 (Hooks、CLI、JSONL log) が durable である。Durable では
  ない — implementation dissolves。各 ADR に込められた *judgment* が
  persist する対象。
- これらの原則が AI 方向性、労働、社会的合意についての文明 scale の
  問題に答える。Open のまま; [`manifesto.md`](manifesto.md) 参照。
- Top-down policy framework が誤っている。それは別の層、別の方法で
  ある。本 repository は bottom — 1 人の operator、1 つの agent、
  動かす摩擦から出てくるもの — の側について。

## 読む順序

新しく入る読者向け:

1. このファイル。
2. [`adr/README.ja.md`](adr/README.ja.md) で索引を見る。
3. [`adr/0001-security-by-absence.ja.md`](adr/0001-security-by-absence.ja.md)
   を最も clean な entry point として — 末尾の audit test がその
   原則を stack に依存せずに具体化する。
4. [`inspiration.md`](inspiration.md) に link された 7 本の essay を
   発表順で読み、ADR に込められた narrative を辿る。
5. [`quadrants/`](quadrants/) を adoption navigator として — decision
   tree、governance mapping、case studies、anti-patterns。
6. [`manifesto.md`](manifesto.md) で work が motivate するが answer
   しようとしない open question を読む。
7. [`social-consequence.ja.md`](social-consequence.ja.md) で社会的
   帰結レイヤー — 内部判断が監査を超えてなぜ重要かを、仕様ではなく
   上位根拠として読む。
