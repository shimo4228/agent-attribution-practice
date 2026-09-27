# AAP: skill repo 反映完遂 + README 全面書き直し + 整合性回復

## Context

`agent-adoption-triage` 単独 skill repo (2026-07-02 公開、`~/MyAI_Lab/agent-adoption-triage`) の AAP への反映は、コミット f462535 で人間向け導線 (README ×2 / CODEMAPS / docs/skills / llms.txt) までは通ったが、**機械可読層 (graph.jsonld / .zenodo.json) と llms-full.txt には未反映**。さらに調査で以下が判明:

1. **実質的な内容 drift**: `docs/quadrants/governance-mapping.md` の Q4 が「every external action passes a human approval gate」と記載するが、ADR-0005 本文は behavior-modifying artifacts のみをゲート対象とし、「every command のゲート」を Alternatives で明示的に rejected。skill 側は ADR 本文に忠実。→ **ユーザー決定: skill 側を正とし、governance-mapping を修正**
2. **README 冗長**: 論文リストが上部 (L9-11) と下部 `## Related publication` (L322-330) に二重掲載、essays も 2 箇所、triage pair 説明も 2 箇所。14 セクション約 350 行。→ **ユーザー決定: 大幅短縮の全面書き直し**
3. **CHANGELOG 遅延**: `[Unreleased]` が EU AI Act 止まりで、以降 ~10 commits (Singapore MGF / codemeta / badges / skill repo / Wikidata QIDs / AI-native preprint) が未記録。「completing the governance-framework triad」の文言も Singapore MGF 追加後は stale。
4. **リリースは今回しない** (ユーザー決定)。CHANGELOG に積むだけ。CITATION.cff version bump / tag / Zenodo DOI / SWH / HF mirror sync は別セッションの release-doi へ。

タスク種別: `writing` (README) + `chore` (docs 整合)。Writing Chain は readme-writer orchestrator に従う。

## 作業項目 (コミット単位)

### Commit 1: fix — governance-mapping の ADR-0005 drift 解消

対象: `docs/quadrants/governance-mapping.md` + `.ja.md`

- L17 (at-a-glance 表 Q4 行): 「human approval on every external action (0005)」→ behavior-modifying writes のゲートに修正
- L99 (Q4 詳細の ADR-0005 行): 「Every external action passes a human approval gate; the alternative is unbounded autonomous side effects.」の文を削除し、ADR-0005 本文 (behavior-modifying artifacts、audit record 必須) に整合する記述へ書き換え
- 日本語版の対応箇所も同時修正
- skill repo 側は**変更なし** (正とする)
- anti-patterns 項目数差 (AAP 11 vs skill 10、skill-internal phase descent の意図的除外) は **no-action** — AAP 内部語彙依存項目の standalone からの除外は編集判断として妥当

### Commit 2: docs — skill repo 2 つを機械可読層へ federate

- **`graph.jsonld`**: `agent-adoption-triage` と `llm-agent-security-principles` (既存の反映漏れ) の 2 skill repo を SoftwareSourceCode ノードとして追加。edges: agent-adoption-triage → ADR-0009/0010 + Quadrant navigator (isBasedOn / about)、security-principles → ADR-0001..0004。既存ノードの `@id` 規約・語彙は `~/.claude/skills/jsonld-knowledge-graph/SKILL.md` に従う
- **`llms-full.txt`**: llms.txt:67 と同等の「Skill references」節を追加 (両 skill repo。既存欠落の解消)
- **`.zenodo.json`**: `related_identifiers` に両 skill repo の GitHub URL を追加 (relation: `isSupplementTo` 系、次回 release で有効化される)
- CITATION.cff / codemeta.json は abstract が既に "a security-principles skill, an adoption navigator" と概念記述済み、version bump は release 時 → 今回触らない
- HF mirror sync は今回実行しない (release セッションで graph ごと同期)

### Commit 3: docs — README 全面書き直し (en 正本 → ja subordinate)

**readme-writer skill を起動**して実行。設計方針:

- 入口特化・大幅短縮 (目安: 350 行 → 120-150 行程度)。LLM が README 一枚で要点を復元できる情報フロアは維持
- **重複統合**: 論文は 1 箇所 (How to Cite 近傍の 1 節に統合、上部は 1 行ポインタ化 or 削除)。essays は `docs/inspiration.md` 委譲で 1 箇所。triage pair 説明は 1 箇所
- **docs/ 委譲**: 「Relationship to AI governance frameworks」(49 行) と「Relationship to industry mechanism layer」(30 行) は各 2-3 行 + `docs/policy-mapping/` / `docs/industry-mapping.md` へのポインタに縮約
- 維持するコア: lead (何のリポで誰向けか) / badges / ten judgments 表 / four Quadrants + Phase 軸の要点 / navigator としての使い方 (walk order) / sibling projects / How to Cite / License
- concept DOI badge (19652013) と versioned DOI (How to Cite 内) の使い分けは維持 (設計上の区別)。混乱防止に How to Cite 側へ「concept vs version」の 1 行注記を追加
- README.ja.md を同構造で追従 (drift させない)
- **レビュー**: readme-reviewer agent 起動 (en/ja 両方) → NEEDS REVISION なら修正、MAJOR ISSUES なら停止・報告
- **Doc Sync**: llms.txt / llms-full.txt の README 参照リンク・記述が新構造と矛盾しないか確認 (memory: この 2 ファイルは反映漏れ常習)

### Commit 4: docs — CHANGELOG [Unreleased] を実履歴に追いつかせる

- 未記録分を追記: Singapore MGF policy mapping (733548d) / harness-alignment paper federation (37a3ad1) / codemeta.json (5c8edc5) / LLM-queryable badges / agent-adoption-triage skill repo cross-link (f462535) / Wikidata 6 QIDs P2860 (57bcd44) / AI-native preprint placements (30d29a7) / 本セッションの governance-mapping fix + graph federation + README rewrite
- 「completing the governance-framework triad」の stale 文を修正 (Singapore MGF で 4 framework)

## Chain / 並列化

- 種別: writing (Commit 3) + chore (Commit 1, 2, 4)
- Parallel Group 1: Commit 1 と Commit 2 の編集は独立 → 並行作業可
- Sequential: Commit 3 (readme-writer → readme-reviewer) は Commit 1/2 の後 (README が graph/skill の最新状態を参照するため)。Commit 4 は最後 (本セッションの変更自体を記録)
- Cross-Model Review: 対象外 (private ドラフトでなく公開 README だが、コード diff なし・prose は readme-reviewer が担当。codex-review は不要と判断)

## Verify (writing 版)

1. 決定論 lint: readme_lint (harness にあれば) / textlint 相当。graph.jsonld は `python -m json.tool` で JSON 妥当性 + jsonld-knowledge-graph skill の検証手順
2. リンク整合: README 内リンク・llms.txt ↔ README ↔ docs/ の相互参照が生きているか grep 確認
3. en/ja README のセクション 1:1 対応確認
4. `git status` — 意図しないファイル混入なし
5. **人間 gate**: 4 コミットの diff をまとめて提示 → 承認後にコミット (push はユーザー確認後)

## 触るファイル一覧

| ファイル | 変更 |
|---|---|
| `docs/quadrants/governance-mapping.md` / `.ja.md` | ADR-0005 drift 修正 |
| `graph.jsonld` | skill repo 2 ノード + edges 追加 |
| `llms-full.txt` | Skill references 節追加 |
| `.zenodo.json` | related_identifiers に skill repo 2 件 |
| `README.md` / `README.ja.md` | 全面書き直し |
| `llms.txt` / `llms-full.txt` | README 新構造との整合確認 (必要なら微修正) |
| `CHANGELOG.md` | [Unreleased] 追いつき + stale 文修正 |

## やらないこと

- skill repo (`agent-adoption-triage`) 側の変更 — 正とするため
- release (tag / Zenodo DOI / CITATION.cff bump / SWH / HF mirror sync) — 別セッション
- anti-patterns 11↔10 項目差の解消 — 意図的キュレーションとして容認
- CITATION.cff / codemeta.json の記述変更 — release 時に一括
