# Plan: CA 観測性 3 skill の AAP 編入 — `agent-observability-patterns` repo 新設

## Context

Contemplative Agent (CA) で実証済みの観測性設計 skill 3 本（replayable-audit-logs / read-only-instruments / shadow-mode-validation、各 CA ADR-0075/0071/0076 系）を、AAP の design-pattern skill モデル（skill 本体は standalone repo、AAP は ADR ペアリング + カタログ + 双方向 cross-link）に乗せて編入する。11 個目の judgment 追加ではなく、既存 judgment（**ADR-0006 Causal Traceability**、**ADR-0005 Human Approval Gate**）への installable な「how」の供給。先例は `llm-agent-security-principles`（1 repo 複数原則）と `agent-adoption-triage`。

- **種別**: `writing`（skill doc / README / llms.txt が一次成果物）+ 少量の `chore`（AAP 側同期）
- **Repo 名（ユーザー確定済み）**: `agent-observability-patterns`（pure-skill repo → `claude-skill-` prefix なし、harness-sync 規約）
- **authorship-strategy rule 適用対象**（AAP は DOI-registered idea-rescue repo）: マネタイズ提案なし、diffusion 指向、vocabulary discipline（coin-sparingly / anchor-densely）

## External Research Findings（Phase 0 相当）

新規実装なし（既存 skill の汎用化 fork + カタログ編入）のため search-first は skip 条件（既存資産の再パッケージ）。vocabulary anchoring のみ実施: "shadow mode" は ML deployment 業界の既存系譜（shadow deployment / shadow testing / dark launch）に anchor する — 造語しない。Verdict 相当: **Extend**（既存 AAP skill-repo モデルの拡張）。

## 成果物 1: 新 repo `~/MyAI_Lab/agent-observability-patterns`

先例 `llm-agent-security-principles` のレイアウトに合わせる（install.sh なし、agents/ なし）:

```
agent-observability-patterns/
├── README.md          # readme-writer チェーンで作成。badge (DeepWiki/GitMCP)、Install 節 (cp ×3 + SkillsMP)、3 パターン表、Lineage 節
├── LICENSE            # MIT
├── CHANGELOG.md       # 0.1.0
├── llms.txt           # llms-txt-writer
├── llms-full.txt      # llms-txt-writer
└── skills/
    ├── replayable-audit-logs/SKILL.md
    ├── read-only-instruments/SKILL.md
    └── shadow-mode-validation/SKILL.md
```

### 汎用化 fork の規約（skills.md Portability 準拠、CA 版と別系統維持 — 丸ごと同期しない）

ソース: `~/MyAI_Lab/contemplative-agent/.claude/skills/{replayable-audit-logs,read-only-instruments,shadow-mode-validation}/SKILL.md`（必要に応じ CA `docs/adr/0071|0075|0076` を参照）

共通ルール:
- CA 固有識別子を除去し抽象 worked example に置換: `MOLTBOOK_HOME` → "the agent's data directory"、`verification-audit.jsonl` / 792 challenges / ADR-0062 → 「外部 verifier に解答を提出する agent」仮想例、`view_metrics.py` / gemma4 swap / cosine 実測値 → 「embedding 類似度の diversity reading」仮想例（数値は illustrative と明記 or 除去）、`circuit_shield` / `skill_selection.py` / T-SKILLSEL / T-M2 / feedback slug（`no-numeric-caps` 等）→ 一般語の原則文に書き換え
- CA ADR 番号 (0023/0036/0050/0051/0062/0071/0072/0075/0076) と AKC ADR 番号を本文から除去。lineage は repo README の Lineage 節に集約（CA / AAP への link はそこだけ）
- 日本語（「計器」、Verify-gate 質問文）→ 英語化（公開 doc は romaji-only 規約、そもそも英語正本）
- 3 skill 間の相互参照は repo 内相対 link に張り替え（同一 repo 内 link は Portability 規約で許容）
- `when-code-when-llm` / `llm-agent-security-principles` への言及は公開 GitHub URL に張り替え
- frontmatter: `origin: shimo4228` 維持、`compatibility:` を CA 固有記述から汎用記述へ、description から CA ADR 番号を除去し汎用トリガーに書き換え
- **vocabulary discipline**: shadow-mode-validation は冒頭で shadow deployment / dark launch 系譜への anchor 文を追加。"instrument" は航空・工学の既存メタファー、"replayable audit log" は append-only log / event-sourcing 系譜 — いずれも造語しない

パターンの核（順序・schema checklist・isolation 教訓・signal-first 両方向・three-point calibration・exit criteria 予約等）は**歪めず**移す — 汎用化はコンテキストの置換であり主張の変更ではない。

## 成果物 2: AAP 側編入（同一 diff で Doc Sync）

| ファイル | 変更 |
|---|---|
| `docs/skills/README.md` | カタログ行追加（Paired ADRs: ADR-0005, ADR-0006）+ Install 節に cp ×3 / SkillsMP 追記 |
| `docs/adr/0005-human-approval-gate.md` + `.ja.md` | Lineage 近傍に skill repo への cross-link 1 文（ADR-0001 の既存形式に倣う） |
| `docs/adr/0006-causal-traceability.md` + `.ja.md` | 同上 |
| `graph.jsonld` | `EcosystemRepo` + `SoftwareSourceCode` ノード追加（先例 2 ノードと同形: `isBasedOn` → ADR-0005/0006 blob URL、`creator` ORCID、`about` → 実装時に vocab の既存 concept を確認して選定、例 causal-traceability 系） |
| `llms.txt` | ecosystem 節（L66-67 付近）に 1 行追加 |
| `llms-full.txt` | L194 の "Two standalone Agent Skill repositories" 段落を three に拡張 + 新 repo の記述追加 |
| `docs/CODEMAPS/architecture.md` | L30 の tree 行 + L70-71 の表に行追加 |
| `codemeta.json` | description の "two standalone installable skill repositories" → three（`.zenodo.json` が存在すれば同様に確認） |
| `README.md` + `README.ja.md` | ecosystem 節（L104-106 付近）に行追加 |
| `CHANGELOG.md` | `[Unreleased]` に追記 |

## チェーン（planning.md writing 種別）

- **doc 分類とルーティング**: 新 repo README → `readme-writer`、llms.txt / llms-full.txt → `llms-txt-writer`、SKILL.md ×3 → 汎用化 fork（skills.md Portability 規約 + skill-creator の記法慣例に従い main context で執筆）
- **Parallel Group 1（drafting）**: SKILL.md ×3 の fork は相互独立 → 並行執筆可。README / llms.txt は skill 確定後に逐次
- **Parallel Group 2（review、同一成果物セットに対し並列）**:
  - `source-fidelity-checker` — fork 版 vs CA 原本の drift 検査（汎用化がパターンの主張を歪めていないか）
  - `readme-reviewer` — 新 repo README（readme_lint 通過後）
  - `codex-review`（prompt-driven モード）— 公開 repo README + SKILL.md は公開前の高 stakes 文書に該当
- **Verify（writing 版）**:
  1. 決定論 lint: readme_lint / geo_check（llms-txt-writer 付属）
  2. **CA 固有識別子の残留 grep**（決定論・code 担当）: `MOLTBOOK_HOME|circuit_shield|view_metrics|verification-audit|skill_selection|Moltbook|T-SKILLSEL|ADR-00(23|36|50|51|62|71|72|75|76)` が新 repo に 0 hit
  3. AAP 側 Doc Sync 完全性確認（上表の全ファイルが同一 diff に含まれる）
  4. `git status` 確認（両 repo）+ secret scan（hook 自動）
- **早期停止**: DRIFT（source-fidelity-checker）/ MAJOR ISSUES / readme_lint FAIL → 停止して報告

## 2 介入点

1. **Plan 確認** — 本 plan（repo 名は確定済み）
2. **公開・commit 直前** — 以下を diff 提示して承認後に実行:
   - `gh repo create shimo4228/agent-observability-patterns --public` + push（外部公開）
   - AAP 側 commit
   - AAP `graph.jsonld` 更新に伴う HF Datasets mirror 同期（`hf-sync` skill、`Shimo4228/agent-attribution-practice`）

## Post（承認後の任意フォロー）

- diffusion ledger（memory）に新 repo 公開を deployed-tactic として 1 行追記
- 新 repo の DOI 化・SkillsMP 掲載等は今回スコープ外（別判断）

## Verification

- 新 repo: 残留 grep 0 hit、readme_lint PASS、3 SKILL.md が frontmatter 込みで valid、相互 link が repo 内で解決
- AAP: 上表 10 ファイルすべて同一 diff、graph.jsonld が `python3 -c "json.load(...)"` で valid、既存先例ノードと同形
- レビュー verdict: source-fidelity DRIFT なし / readme-reviewer NEEDS REVISION 以下 / codex-review CRITICAL なし
