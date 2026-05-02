言語: [English](0008-one-agent-one-human.md) | 日本語

# ADR-0008: One Agent, One Human (experimental)（1 つのエージェントに 1 人の人間 — experimental）

> **Summary。** Agent process は、それに対する accountability を持つ
> 1 人の identifiable な人間に bind される。その人間が behavior-modifying
> な変更を承認 (ADR-0005) し、incident response を所有し、accountability
> chain の終端となる。「The team」は agent の人間ではない; agent
> instance ごとに特定の named person が agent の人間。

## Status
**experimental** — この原理は ADR-0004 (1 つの agent に 1 つの external
adapter) と accountability-wall thesis から構造的に導かれるが、team
運用の failure mode に対してまだテストされていない。末尾の open
questions 参照。

## Date
2026-04-19 (本 repository での初 formalize)

## Context

Accountability-wall essay は、AI agent が組織で adoptable であるために
は「誰が責任を負うか」が答えを持たねばならないと argue した。技術側
の明らかな並行 — 職務分掌、capability あたり 1 process — は ADR-0004
(Single External Adapter per Agent) で formalize された。だが ADR-0004
は *capability* を分割するもの。人間側で誰が accountable かは specify
していない。

Accountability endpoint を produce しない common pattern:

- **「Team が agent を所有する。」** チームの 5 人全員が等しく responsible
  なら、誰も specifically responsible ではない。Incident retrospective で
  accountability は組織的抽象に拡散する。
- **「Agent は autonomous。」** Operational loop に named human がいない
  まま deploy される。何かが起きると chain は「モデルがやった」か
  codebase に最後に触ったエンジニアで終わる。どちらも有意な意味での
  accountability ではない。
- **「AI が AI を supervise する。」** 2 つ目の agent (reviewer、critic、
  guardrail LLM) が 1 つ目を gate する。両方とも確率的 system。
  Accountability chain は人間に到達せずに循環する。

構造的観察: agent process が外部 side effect を multiplex すべきでない
(ADR-0004) のと同じく、agent process は人間 accountability endpoint を
multiplex すべきではない。Multiplex された accountability は拡散した
accountability。

## Decision (experimental)

各 agent process は deployment 時に、accountable operator となる 1 人
の identifiable な人間に bind される。その人物は:

- **Behavior-modifying changes を approve する** (ADR-0005)。Approval
  prompt が発火する時、特定の named human が approver。Group mailbox、
  rotation、LLM ではない。
- **その agent の incident response を所有する。** Postmortem が書か
  れる時、accountable operator が endpoint — 必ずしも「at fault」な
  party ではないが、agent の振る舞いについて answer する party。
- **Agent の operational record に named されている。** Accountable
  operator の変更それ自体が behavior-modifying な変更で、退任 operator
  と着任 operator 双方の approval を要求する。

### What this permits（これが許すこと）

- **Rotation を伴う team 運用** は、rotation が formal なら compatible:
  任意の時点で正確に 1 名の named person が accountable operator で
  あり、handoff は明示的で log される。これは SRE の on-call pattern を
  agent に適用したもの。
- **Escalation chain** は compatible。Accountable operator は決定を
  上司に escalate しうるが、上司がその決定の accountable operator に
  なる (handoff は log される)。
- **1 人の人間に複数の agent。** 1 人の人間が多数の agent の
  accountable operator になりうる。Binding は一方向: 1 agent → 1
  human、1 human → 1 agent ではない。

### What this forbids（これが禁じること）

- **Committee approval を default path とすること。** Approval が
  committee を必要とするなら、committee は各決定について 1 名の
  member を accountable operator に指名する。Committee は inform
  しうる; endpoint にはなれない。
- **LLM が accountable operator となる。** 確率的 system は accountability
  chain の endpoint になれない。Chain は人間で終端しなければならない。
- **匿名または未解決の operator。** Agent process が任意の瞬間に
  現 accountable operator を named できないなら、次の approval gate
  で halt する。

### Behavior on operator unavailability（operator 不在時の振る舞い）

Accountable operator が不在 (休暇、無能力化、退職) で handoff が起きて
いない場合、agent は許可的振る舞いに default するのではなく次の
approval gate で halt する。「人間が利用不可」は valid な答え;
「だから人間なしで進める」は valid ではない。

## Alternatives Considered

- **人間 binding なし; 認可された任意の operator が approve できる**
  — Default として reject。Binding なしでは approval が「たまたま近く
  にいた誰か」になり、postmortem に明確な endpoint がない。
- **個人ではなく role への binding** (例えば「the security engineer」)
  — 弱い。Role は occupant が変わり、responsibility が transition 中に
  拡散し、role-to-person 解決は accountability が必要な瞬間にこそ
  暗黙になる。
- **Team への collective sign-off による binding** — Default として
  reject。Logged collective sign-off は named individual より弱い、
  なぜなら incident review に明確な endpoint がないから。Team は依然
  上記の rotation を介して運用しうる。
- **別の agent (AI supervisor) への agent の binding** — Reject。
  Accountability chain は人間で終端しなければならない。第二の agent
  は review を補助しうる; accountable operator に *なる* ことはでき
  ない。

## Open questions (why this is experimental)（なぜ experimental か）

1. **Enterprise への scaling。** ほとんどの enterprise deployment は
   team 所有を仮定する。1-人間 binding は scale で friction になるか、
   そうなら何人規模の組織で? Formal rotation が十分な答えになるかは
   不明。
2. **Time zone と連続運用。** 24/7 の agent は 1 人の人間を待てない。
   Rotation がこれを handle するが、handoff 頻度と protocol は経験的
   テストが要る。
3. **複数の人間に跨る決定タイプ。** 一部の behavior-modifying な
   変更は論理的に複数 stakeholder (legal、security、product) を
   伴う。「決定ごとに 1 名の approver、他者は inform される」が正しい
   pattern なのか、それとももっと structured なものが要るのか?
4. **ADR-0004 との interaction。** 1 agent = 1 adapter = 1 human
   なら、multi-surface system のための composition rule が複雑になる。
   正しい grouping はまだ obvious ではない。
5. **Revocation と departure。** Accountable operator が組織を去る
   時、agent には何が起きるか? Handoff protocol を定義しなければなら
   ない; 現状は未指定。

この ADR は運用経験が蓄積するに従って改訂される。Open question が
documented answer を持つようになれば status は experimental から
accepted に動き、binding pattern が誤りだとテストで判明すれば
rejected / superseded に動く。

## Consequences

- Accountability chain が agent instance ごとに明確な endpoint を
  持つ。Postmortem が「the team」で終わらない。
- Agent を deploy するには人間を名指す必要がある。これは deployment
  時のコスト; 一度払えば後の曖昧さを防ぐ。
- Agent が「orphan」化できない。現 accountable operator のいない
  agent は次の approval gate で halt し、組織に operator を assign
  するか agent を retire するかを迫る。
- Binding は agent を named tool にする: *the research agent* ではなく
  *Alice's research agent*。これは mental model を「infrastructure」
  から「委任された agency」に shift する — それが agent の実体。

---

**Lineage。** この原理の germ は [the accountability-wall essay](https://github.com/shimo4228/zenn-content/blob/main/articles-en/ai-agent-accountability-wall-en.md) (2026-04-06) に登場する:
> 「責任を負える人間がループに入る構造」
> (a structure in which a human capable of bearing responsibility enters the loop)

この ADR はそのフレーズを ADR-0004 と並ぶ構造的 rule として formalize
する最初の試み。複数人組織設定ではまだ validate されていない。
