言語: [English](0001-security-by-absence.md) | 日本語

# ADR-0001: Security by Absence as a First-Class Principle（不在による安全 — 一級原理として）

> **Summary。** 危険なエージェント capability — シェル実行、任意の
> ネットワークアクセス、ファイルシステム traversal、動的コード評価 —
> は制限されない。最初から実装されない。Prompt injection は harness が
> もともと持っていない能力を grant することはできない。

## Status
accepted

## Date
2026-04-09 (decided) / 2026-04-19 (本 repository で再表現)

## Context

AI agent は時間と共に capability を蓄積する: シェル実行、任意の HTTP、
ファイルシステム traversal、credential アクセス。各 capability は新しい
attack surface になる。Prompt injection — 文書化が進んでいる脅威 — は、
agent が持つ任意の capability を、agent がそれをどう使うように指示
されているかに関係なく、exploit vector に変えうる。

通常の対応は危険な capability を **制限** すること: allowlist、
sandbox、permission prompt、guardrail LLM。これらの方法は既知の
misuse パターンには効くが、制限層自体が bypass されたり misconfigured
だったりした時に fail open する。さらに追加した制限はそれ自体が、
保守し、テストし、対象 capability と同期させ続けないといけない
コードでもある。

Auditable で長持ちすることを目指す harness には、「制限される」より
強くて単純な保証が要る。

## Decision

本プロジェクトは **Security by Absence（不在による安全）** を一級
設計原理として採用する:

> 危険な capability は制限されない。それらは **最初から実装されない**。

Harness は、シェルコマンドを実行するコード、任意の network 接続を
開くコード、狭く定義された data directory の外のファイルシステムを
traversal するコードを ship しない。Prompt injection は harness が
もともと持っていない能力を grant することはできない。

### What is not implemented（実装しないもの）

| Capability | Status |
|------------|--------|
| Agent core からのシェル / subprocess 実行 | Absent |
| 任意の outbound HTTP | Absent (固定された single adapter のみ; ADR-0004 参照) |
| Agent の data directory 外へのファイル書き込み | Absent |
| Agent の data directory 外への任意のファイル読み取り | Absent |
| 動的コード読み込み / LLM output の `eval` / `exec` | Absent |
| Network から到達可能な control plane / remote command channel | Absent |

### What this defends against（これが防ぐもの）

- **Prompt injection escalation** — log、knowledge、外部入力に含まれる
  悪意ある content は、codebase に存在しない capability を呼び出せ
  ない。
- **Supply chain drift** — 依存が compromise されても、harness 自体に
  patch が当たらない限り新しい attack surface を獲得できない。
- **Misconfiguration failure modes** — 制限する対象がないため、
  misconfigure する制限層が存在しない。
- **Audit ambiguity** —「capability X は存在するか?」が `grep` で
  答えられる。

### What this does NOT defend against（これが防がないもの）

- **LLM-generated false outputs** — harness は LLM が許可された
  output channel 内で誤った・誤解を招くテキストを生成することを
  防がない。
- **Data exfiltration through the pinned adapter** — 単一の external
  adapter が存在する場合 (ADR-0004)、それを通じて書き込まれる content
  はなお漏れうる。
- **Local privilege issues** — ファイル権限、プロセス分離、OS レベル
  ハードニングは operator の責任。
- **Prompt injection influencing content** — injected text は agent
  が許可された output channel を通じて *言うこと* をなお変えうる;
  ただし新しい capability を獲得することはない。

### Applying the principle（原理の適用）

新しい module、依存、capability を追加する前に問う:

1. これは harness が以前にはできなかった新しい side effect を導入
   するか? (シェル、network、data dir 外のファイル書き込み、動的
   コード)
2. Yes なら、攻撃者が制御する input がそこに到達しうる脅威モデルは
   存在するか? Yes なら **追加しない**。それを必要としない設計を
   見つける。
3. Capability が本当に避けられない場合、prohibition は ADR-0002
   (Deterministic Prohibition at the Scaffolding Layer) に降りる。
   外部 side effect については、capability はさらに ADR-0004 (Single
   External Adapter) と ADR-0005 (Human Approval Gate) を通る必要が
   ある。

## Alternatives Considered

- **Restrict at runtime (sandboxes, allowlists)** — 既知パターンには
  効くが、misconfiguration 時に fail open し、保守すべきコードを
  足す。
- **Guardrail LLM / policy model** — 確率的であって決定論的では
  ない。Capability gating には不十分。
- **Capability tokens / permission prompts** — 負担をユーザに移し、
  prompt injection が user に approve するよう説得することに対して
  脆弱。

## Consequences

- Harness は仕様、schema、最小の reference example として ship される
  — 機能豊富な agent framework としてではない。
- 便利な機能 (one-shot shell、open HTTP) は意図的に利用不可。必要な
  ユーザは自前で adapter を build しその risk を持つ。
- Code review に単純な rule が追加: 「この PR は新しい side-effect
  type を追加するか?」 Yes ならその PR は新しい ADR を要求するか
  reject される。

## Audit test

Security by Absence は `grep` で audit できる。Codebase が clean
なら、1 コマンドで 0 件 (または ADR が明示許可した review 済み
adapter 内のヒットのみ) が返る:

```bash
git grep -nE "subprocess|os\.system|\beval\(|\bexec\(|urlopen|requests\.(get|post|put|delete|patch)" \
  -- ':!docs' ':!examples' ':!tests'
```

除外をプロジェクトに合わせて調整する。Rule は: **「capability X は
存在するか?」の問いに `grep` で答えられないなら、あなたの system は
Security by Absence を持っていない — 持っているのは希望だ。** Grep
を CI に追加してこの check を決定論にする。

完全な「how」 reference は
[llm-agent-security-principles](https://github.com/shimo4228/llm-agent-security-principles)
skill（旧 `docs/skills/llm-agent-security-principles.md`、[`docs/skills/`](../skills/README.md) 参照）
を参照。Audit-test の根拠、HTTP と credential ハードニング pattern、
design-review checklist を含む。

---

**Lineage。** 脅威モデルは [contemplative-agent `docs/adr/0007-security-boundary-model.ja.md`](https://github.com/shimo4228/contemplative-agent) で最初に登場した。[Agent Knowledge Cycle](https://github.com/shimo4228/agent-knowledge-cycle) v1.x で独立した原理に generalized され、AKC v2.0.0 が mechanism-only に再ポジショニングされた 2026-04-19 に本 repository に抽出された。
