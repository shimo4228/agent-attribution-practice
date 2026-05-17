言語: [English](README.md) | 日本語

# AI ガバナンス framework マッピング (AI governance framework mapping)

**Status:** Informational, time-bound (2026 Q2 reading)
**Date:** 2026-05-17
**Maintenance posture:** This directory decays. The ADRs do not.

## Purpose

本 repository の ADR は autonomous AI agent システムにおける
attribution の分配に関する判断を記録する。各国 / 国際の AI
governance framework (NIST AI RMF、ISO/IEC 42001、EU AI Act、OECD
AI Principles) は、それら判断を読み合わせる対象となる *governance
framework layer* ── control catalog、management-system 要件、リスク
分類、原則表明 ── を ship する。本ディレクトリは執筆時点で、AAP
の 10 ADRs と 4 Business AI Quadrants が各 framework の具体的な
条項とどう対応するかを記録する。

ADR 本文に埋め込まずに別ディレクトリに切り出した理由は、本 repository
の core thesis を遵守するため: *implementation dissolves; judgment
persists* (実装は溶ける、判断は残る)。Framework は改訂・置換・番号
振り直し・廃止されるが、ADR はそうならない。framework の進化に応じて
本ディレクトリだけ更新すれば、ADR には触れずに済む。

本ディレクトリは
[*industry mechanism layer mapping*](../industry-mapping.ja.md)
(vendor 製品 ── Microsoft Agent 365、Agent Governance Toolkit、
Purview 等 ── を ADR に対応づける別ディレクトリ) の
**governance framework layer** 側の対をなす。2 つのディレクトリは
sibling であり、対象とする reader (policy / compliance 担当 vs
エンジニア / アーキテクト) も decay 速度も異なる。

## 本ディレクトリ内のファイル

| File | Framework | Framework version | Maintenance cadence |
|---|---|---|---|
| [nist-ai-rmf.md](nist-ai-rmf.md) (+ [ja](nist-ai-rmf.ja.md)) | NIST AI Risk Management Framework | RMF 1.0 (2023-01) + Generative AI Profile NIST.AI.600-1 (2024-07) | 年単位 (core RMF より GAI Profile の方が改訂頻度が高い見込み) |
| [iso-iec-42001.md](iso-iec-42001.md) (+ [ja](iso-iec-42001.ja.md)) | ISO/IEC 42001 AI Management System | ISO/IEC 42001:2023 | 数年単位 (ISO の正式改訂サイクル) |

EU AI Act と OECD AI Principles の mapping は後続 release に延期する
(両者は NIST/ISO とは decay cadence と粒度が十分異なるため、同じ
release にまとめると noise が混ざる)。

## Direction convention (方向の規約)

**ADR 中心の primary direction**。各 framework file は ADRs
0001–0010 を順に列挙し、それぞれの ADR の判断がその framework の
どの条項を instantiate するかを記述する。AAP 側の anchor は
framework 改訂を跨いで安定する。

**Reverse index の secondary direction**。各 framework file の末尾
に reverse index (framework 条項 → 該当 ADR) を載せ、framework 側
から来る reader ── NIST control owner、ISO 42001 監査人、EU AI Act
compliance officer ── にも citation entry を提供する。

## Framework version pinning convention

各 framework file の冒頭で、その file が前提とする **正確な
framework version / profile / 公開日** を pin する。例:

> *Framework version: NIST AI Risk Management Framework 1.0
> (NIST.AI.100-1, 2023-01) + Generative AI Profile (NIST.AI.600-1,
> 2024-07).*

framework が改訂版を出したら、その framework に対応する file だけ
を更新する。ADR には触れない。framework 内の cross-version diff
が ADR 本文に伝播することは、いかなる状況でも起こさない (下記
*Revision procedure* 参照)。

## Revision procedure

framework が改訂版を公開した場合:

1. 対応する framework file (例 `nist-ai-rmf.md`) を開き、ヘッダの
   `Framework version:` / `Date:` を更新する。
2. ADR ごとの mapping を walk し、新 framework version で移動 /
   改名 / 廃止された条項を引いている箇所を修正する。
3. Reverse index を更新する。
4. 日本語 mirror を同期する。
5. **`docs/adr/` 配下のファイルには触らない**。ADR の役目は判断
   の記録であり、今四半期にどの framework が何を cite している
   かを追跡することではない。
6. commit message に framework + version を明示する (例:
   `docs(policy-mapping): refresh against NIST GAI Profile
   2026-Q4 revision`)。

framework が AAP の既存 ADR に対応する判断を持たない新条項を導入
した場合、**ADR 側を遡及的に拡張することはしない** ── 新しい判断
を first principles から記録する新規 ADR を立てる (固有の Lineage
付き) か、mapping file が gap を明示する。mapping coverage を目標
にはしない。

## Non-attestation disclaimer (template — 本ディレクトリの全 file に適用)

> **本記述は AAP の reading である**。以下の per-ADR mapping は、
> AAP の ADR と当該 framework の 1 つ以上の条項との関係を AAP
> がどう読んでいるかを記録する。compliance attestation ではない:
> ADR の採用が framework 条項の充足を保証する、あるいはその逆を
> 保証する、と AAP は主張しない。legal advice ではない。framework
> の authoritative interpretation は framework body (NIST、
> ISO/IEC、欧州委員会、OECD) と deploying organization の
> jurisdiction における qualified legal / compliance counsel に
> 残る。mapping は cross-reference のための citation surface
> として提供されるものであって、framework text 自体の代替では
> ない。

各 framework file は自身のヘッダにこの disclaimer を再掲する。

## Sibling: industry mechanism layer

[`../industry-mapping.ja.md`](../industry-mapping.ja.md) は本
ディレクトリの *vendor 製品* 側の対をなす。出荷中の vendor
mechanism (sub-millisecond ポリシーゲート、agent identity
primitive、sponsor 制度、cross-vendor 監査、registry sync) を ADR
に対応づける。2 つの layer は AAP landscape の異なる部分を populate
する:

- *Governance framework layer* (本ディレクトリ) ── 規制当局 /
  標準化団体 / management-system 監査が cite する対象。framework
  改訂サイクル (年単位〜数年単位) で decay する。
- *Industry mechanism layer* ── vendor 製品が ship する対象。
  製品 release サイクル (四半期以下) で decay する。

任意の ADR は、ある時点で 1 layer のみ / 両 layer / どちらにも
entry がない、いずれの状態でも成り立つ。ADR 自体は durable な
anchor として残る。

## 本ディレクトリが主張しないこと

- AAP の ADR が列挙 framework のすべての条項を cover する、ということ。
  ADR は実装から抽出されたもの、framework は政策から起草されたもの。
  coverage は両方向で部分的。
- framework の条項が、対応する AAP ADR を完全に表現している、ということ。
  judgment layer (いつ / なぜ / どのコストで) は通常 framework 条項
  本文よりも先に踏み込む。
- 列挙した framework が、ある特定 jurisdiction にとって唯一・もしくは
  最も authoritative な framework である、ということ。選定は現在の
  LLM-mediated citation surface を反映したものであり、normative ranking
  ではない。

## Caveat

ここに列挙する framework は改訂 / 番号振り直し / 置換 / 廃止される。
それらが対応する ADR はそうならない。framework の進化に応じて本
ディレクトリを update する; framework の変更を ADR に逆伝播させて
はならない。
