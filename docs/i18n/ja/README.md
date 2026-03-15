# AI Dev OS Plugin — Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](../../../LICENSE)

**AI Dev OS** の4層モデルを Claude Code の Skills / Hooks / Sub-agents として実行可能にするプラグインです。

**[AI Dev OS](https://github.com/yunbow/ai-dev-os) エコシステムの一部です** — 暗黙知を強制可能なAIコーディングルールに変換するフレームワーク（[Lifespan Layers](https://github.com/yunbow/ai-dev-os#lifespan-layers--the-4-layer-model)）。
プロジェクトに [AI Dev OS Rules](https://github.com/yunbow/ai-dev-os-rules-typescript) がセットアップされている必要があります。

## なぜこのプラグインか？

AI Dev OS のガイドラインを Claude Code の**自動化ワークフロー**に変換します:

- **11 Skills** — `/ai-dev-os-check`、`/ai-dev-os-scan`、`/ai-dev-os-extract` など
- **3 Sub-agents** — 哲学アドバイザー、原則チェッカー、ガイドライン監査
- **Lifecycle Hooks** — コード変更時の自動チェック、コミット前のリマインド
- **ワンコマンドセットアップ** — `npx ai-dev-os init --rules typescript --plugin claude-code`

## クイックスタート

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

> submodule の追加、CLAUDE.md テンプレートのコピー、hooks のマージを自動で実行します。
> 詳細は [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli) を参照。

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0 と、プロジェクトに AI Dev OS レイヤーファイル（L1-L3）が必要です（[TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)）。

<details>
<summary>手動セットアップ</summary>

```bash
# 1. AI Dev OS ルールを submodule として追加
git submodule add https://github.com/yunbow/ai-dev-os-rules-typescript.git docs/ai-dev-os
# Python プロジェクトの場合:
# git submodule add https://github.com/yunbow/ai-dev-os-rules-python.git docs/ai-dev-os

# 2. このプラグインを submodule として追加
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

3. `hooks/hooks.json` の内容をプロジェクトの `.claude/settings.json` にコピー
4. **Claude Code チャット**で `/ai-dev-os-init` を実行して4層構造をセットアップ
5. コーディング開始 — フックが自動的にガイドします

詳細は[運用手順書](./docs/operation-guide.md)を参照してください。

</details>

## Skills（スキル）

| スキル | コマンド | 説明 |
|--------|---------|------|
| **Init** | `/ai-dev-os-init` | 初期化ウィザード — 30分でプロジェクトにAI Dev OSを導入 |
| **Check** | `/ai-dev-os-check` | ガイドライン準拠チェック（`git diff`、`--staged`、ブランチ比較に対応） |
| **Scan** | `/ai-dev-os-scan` | プロジェクト全体の全ソースファイルに対する準拠スキャン |
| **Review** | `/ai-dev-os-review` | PR前セルフレビュー — L3準拠 + L2設計レビュー + L1整合性を統合 |
| **Extract** | `/ai-dev-os-extract` | コードレビューの差分から新ルールを抽出（ルールハーベスティング） |
| **Why** | `/ai-dev-os-why` | ルールの根拠をL3→L2→L1まで遡って説明 |
| **Plan** | `/ai-dev-os-plan` | 実装前にガイドラインチェックリスト付きの実装計画を作成 |
| **Ticket** | `/ai-dev-os-ticket` | 実装概要とチェックリスト候補を含むチケットを生成（ローカルファイルまたはGitHub Issue） |
| **Audit** | `/ai-dev-os-audit` | 4層の健全性を監査: 依存性ルール、鮮度、カバレッジ、一貫性 |
| **Evolve** | `/ai-dev-os-evolve` | 直近のコミットを分析し、L1-L2の更新を提案（SECI螺旋） |
| **Report** | `/ai-dev-os-report` | チーム・ステークホルダー向けの準拠サマリーレポートを生成 |

## Sub-agents（サブエージェント）

| エージェント | モデル | 役割 |
|------------|--------|------|
| `philosophy-advisor` | Opus | L1哲学に基づくアーキテクチャ判断支援 |
| `principle-checker` | Sonnet | コード変更のL2原則との整合性検証 |
| `guideline-auditor` | Sonnet | L3ガイドラインの網羅性・一貫性・鮮度監査 |

## Hooks（フック）

| イベント | トリガー | アクション |
|---------|---------|-----------|
| PreToolUse (Write/Edit) | コード変更時 | ガイドライン準拠の簡易チェック |
| PreToolUse (Bash: git commit) | コミット前 | `/ai-dev-os-check` 実行のリマインド |
| PostToolUse (Write/Edit) | L1-L2ファイル変更後 | 依存性ルール違反の警告 |

<details>
<summary>パッケージ構造</summary>

```
ai-dev-os-plugin-claude-code/
├── .claude-plugin/
│   └── plugin.json              # プラグインマニフェスト
├── skills/
│   ├── ai-dev-os-init/          # 初期化ウィザード
│   ├── ai-dev-os-check/         # ガイドライン準拠チェック（git diff）
│   ├── ai-dev-os-scan/          # プロジェクト全体の準拠スキャン
│   ├── ai-dev-os-review/        # PR前セルフレビュー（L1-L3）
│   ├── ai-dev-os-extract/       # 逆算方式ルール抽出
│   ├── ai-dev-os-why/           # ルールの根拠説明（L3→L2→L1）
│   ├── ai-dev-os-audit/         # 4層健全性監査
│   ├── ai-dev-os-plan/           # ガイドライン準拠の実装計画
│   ├── ai-dev-os-ticket/        # チケット生成（実装概要＋チェックリスト候補）
│   ├── ai-dev-os-evolve/        # SECI螺旋フィードバック（L4→L1）
│   └── ai-dev-os-report/        # 準拠レポート生成
├── agents/
│   ├── philosophy-advisor.md     # L1哲学に基づく判断支援
│   ├── principle-checker.md      # L2原則の整合性検査
│   └── guideline-auditor.md      # L3ガイドライン監査
├── hooks/
│   └── hooks.json                # ライフサイクルイベントフック
├── templates/
│   ├── CLAUDE.md.template        # CLAUDE.mdテンプレート
│   ├── ai-dev-os-starter/        # 最小構成テンプレート
│   └── ai-dev-os-full/           # フル4層テンプレート
└── settings.json                  # デフォルト権限設定
```

</details>

## 優先度カスケード

ルールが競合した場合: フレームワーク固有 > 共通 > プロジェクト固有 > 判断基準 > 哲学。[→ 詳細](https://github.com/yunbow/ai-dev-os/blob/main/spec/priority-cascade.md)

## 関連リポジトリ

| リポジトリ | 説明 |
|---|---|
| [ai-dev-os](https://github.com/yunbow/ai-dev-os) | フレームワークの仕様と理論 |
| [ai-dev-os-rules-typescript](https://github.com/yunbow/ai-dev-os-rules-typescript) | TypeScript / Next.js / Node.js ガイドライン |
| [ai-dev-os-rules-python](https://github.com/yunbow/ai-dev-os-rules-python) | Python / FastAPI ガイドライン |
| [ai-dev-os-plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) | Kiro 向け Steering Rules と Hooks |
| [ai-dev-os-plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) | Cursor Rules (.mdc) |
| [ai-dev-os-cli](https://github.com/yunbow/ai-dev-os-cli) | セットアップ自動化 — `npx ai-dev-os init` |

## ライセンス

[MIT](../../../LICENSE)

---

Languages: [English](../../../README.md) | 日本語 | [简体中文](../zh-CN/README.md) | [한국어](../ko/README.md) | [Español](../es/README.md)
