# AI Dev OS Plugin — Claude Code — 運用手順書

## 目次

1. [前提条件](#1-前提条件)
2. [インストール](#2-インストール)
3. [初期セットアップ](#3-初期セットアップ)
4. [日常ワークフロー](#4-日常ワークフロー)
5. [定期メンテナンス](#5-定期メンテナンス)
6. [チームオンボーディング](#6-チームオンボーディング)
7. [言語ポリシー](#7-言語ポリシー)
8. [CI 統合](#8-ci-統合)
9. [トラブルシューティング](#9-トラブルシューティング)

---

## 1. 前提条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0
- プロジェクトに AI Dev OS のレイヤーファイル（L1-L3）がセットアップ済みであること（テンプレート: [TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)）

---

## 2. インストール

### 方法A: CLI（推奨）

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

submodule の追加、CLAUDE.md テンプレートのコピー、hooks のマージを自動で実行します。詳細は [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli) を参照。

### 方法B: Git Submodule

AI Dev OS の更新をプロジェクトと分離して管理できます:

```bash
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

### 方法C: 直接クローン

```bash
git clone https://github.com/yunbow/ai-dev-os-plugin-claude-code.git
cp -r ai-dev-os-plugin-claude-code/skills/ .claude/skills/
cp -r ai-dev-os-plugin-claude-code/agents/ .claude/agents/
```

### フックの設定

`hooks/hooks.json` のフック定義をプロジェクトの `.claude/settings.json` にコピーします:

```json
{
  "permissions": { ... },
  "hooks": {
    "PreToolUse": [ ... ],
    "PostToolUse": [ ... ],
    "UserPromptSubmit": [ ... ]
  }
}
```

完全なフック設定は `hooks/hooks.json` を参照してください。

#### フックマージガイド

`.claude/settings.json` が既に存在し、他の設定を含んでいる場合は、フックを手動でマージしてください:

**方法A: 手動マージ**

1. `hooks/hooks.json` と `.claude/settings.json` を並べて開く
2. `hooks/hooks.json` から各配列（`PreToolUse`, `PostToolUse`, `UserPromptSubmit`）をコピー
3. `settings.json` に既に `hooks` キーがある場合は、各既存配列に**追加**する
4. `settings.json` に `hooks` キーがない場合は、`hooks` オブジェクト全体を追加する

**方法B: jq を使用（Unix/macOS）**

```bash
jq -s '.[0] * {hooks: (.[0].hooks // {} | to_entries + (.[1].hooks | to_entries) | group_by(.key) | map({(.[0].key): [.[] | .value[]] }) | add)}' .claude/settings.json hooks/hooks.json > .claude/settings.merged.json
mv .claude/settings.merged.json .claude/settings.json
```

**確認**: マージ後、`settings.json` の `hooks` キーの下に `PreToolUse`、`PostToolUse`、`UserPromptSubmit` の各配列が含まれていることを確認してください。

---

## 3. 初期セットアップ

### セットアップウィザードの実行

```
/ai-dev-os-init [tech-stack]
```

例:
```
/ai-dev-os-init nextjs
```

ウィザードの動作:
1. 技術スタック、プロジェクト規模、既存ルールファイルをヒアリング
2. 既存ルールの検出と取り込み（`.cursorrules`, `CLAUDE.md`, `.eslintrc`）
3. 4層ディレクトリ構造の生成
4. ガイドライン参照と優先度カスケードを含む `CLAUDE.md` の作成
5. Git Submodule 分離の設定（オプション）

### セットアップの確認

初期化後、以下の構造を確認します:

```
ai-dev-os/
├── 01_philosophy/
├── 02_decision-criteria/
├── 03_guidelines/
│   ├── common/
│   └── frameworks/
└── 04_ai-frames/
```

---

## 4. 日常ワークフロー

### 4.1 実装前の計画

重要な変更の前に、ガイドライン準拠の計画を作成します:

```
/ai-dev-os-plan JWT認証機能を追加
```

実行内容:
1. リクエストを分析し、影響を受けるファイルを特定
2. ファイルと関連する AI Dev OS ガイドラインをマッピング
3. 適用可能なルールのチェックリストを生成
4. コードを書く前にプランを提示して承認を得る

フックが実装関連のプロンプトを検出すると、`/ai-dev-os-plan` の利用を提案します。

### 4.2 実装せずにチケットを作成する

実装を後回しにしたい場合（チームへのアサインやバックログ管理など）、チケットを作成します:

```
/ai-dev-os-ticket JWT認証機能を追加
```

実行内容:
1. `/ai-dev-os-plan` と同じ分析を実行（影響ファイル、ガイドラインマッピング、チェックリスト）
2. チケットの出力先を確認（CLAUDE.md に設定がない場合）:
   - **ローカルファイル**: 指定ディレクトリに `TICKET-001-add-user-auth.md` として保存
   - **GitHub Issue**: `gh issue create` で Issue を作成
3. プレビューを表示して承認を得る
4. チケットを作成 — **コードは書かない**

チケットの出力先を事前設定するには、プロジェクトの `CLAUDE.md` に追加します:

```markdown
## Ticket Settings
- output: file
- path: docs/tickets/phase1
```

GitHub Issue の場合:

```markdown
## Ticket Settings
- output: github-issue
```

### 4.3 コーディング

通常通りコードを書きます。フックが自動的に:
- Write/Edit のたびにガイドライン準拠をチェック
- L1-L2ファイル編集時に依存性ルール違反を警告
- コミット前に準拠チェックの実行をリマインド

### 4.4 コミット前

準拠チェックを実行します:

```
/ai-dev-os-check
```

実行内容:
1. `CLAUDE.md` をパースして適用可能なガイドラインを検出
2. `git diff` から変更ファイルを取得
3. ファイルと関連ガイドラインのマッピング構築
4. 準拠チェックを実行してレポート出力

### 4.5 コードレビュー後

AIが生成したコードをレビューで修正した場合、新ルールを抽出します:

```
/ai-dev-os-extract [file-path]
```

実行内容:
1. 差分を分析して「なぜ変更したか」を特定
2. `MUST` / `MUST NOT` 形式でルール候補を生成
3. 追加先ガイドラインファイルとL2原則リンクを提案
4. 承認後にガイドラインへ追記

### 4.6 ルールの理解

チームメンバーがルールに疑問を持った時:

```
/ai-dev-os-why [rule-or-guideline]
```

例:
```
/ai-dev-os-why "なぜany型が禁止なの？"
```

---

## 5. 定期メンテナンス

### 月次: 健全性監査

```
/ai-dev-os-audit
```

チェック項目:
- 依存性ルール（L1にツール固有語がないか、L2にフレームワーク詳細がないか）
- 賞味期限（L1: 5年、L2: 3年、L3: 12ヶ月、L4: 4ヶ月）
- トレーサビリティ（L3→L2リンク、孤立ルール）
- カバレッジ（全ファイルパターンにガイドラインがあるか）
- 一貫性（矛盾するルールがないか）

### 四半期: SECI螺旋進化

```
/ai-dev-os-evolve
```

直近のコミットとレビューパターンを分析し、L1哲学・L2原則の更新を提案します。これは「知識螺旋」— 日常の実践から暗黙知を形式知に変換するプロセスです。

### 週次/月次: 準拠レポート

```
/ai-dev-os-report 1w
/ai-dev-os-report 1m
```

チームミーティングやステークホルダー報告に適したサマリーレポートを生成します。

---

## 6. チームオンボーディング

### 新メンバー向け

1. `ai-dev-os/01_philosophy/core-values.md` を読む（5分）
2. `ai-dev-os/02_decision-criteria/` をざっと確認（10分）
3. 不明なルールには `/ai-dev-os-why` を実行
4. コーディング開始 — フックが自動的にガイド

### AI Dev OS を導入するチーム向け

1. プロジェクトで `/ai-dev-os-init` を実行
2. **starter** テンプレート（L3のみ）から開始
3. コードレビューのたびに `/ai-dev-os-extract` を使用
4. 2-4週間後に `/ai-dev-os-audit` でギャップを特定
5. `/ai-dev-os-evolve` で L1-L2 を段階的に構築

---

## 7. 言語ポリシー

プラグインのソースファイル（skills, agents, hooks, templates）はすべて**英語**で管理します。これはLLMの精度を最大化し、幅広いユーザーがアクセスできるようにするためです。翻訳ドキュメントは `docs/i18n/` に参考として提供しますが、英語が唯一の正（Single Source of Truth）です。

---

## 8. CI 統合

フックは開発中のリアルタイムなアドバイスを提供しますが、AIコーディングツール内でのみ動作します。リポジトリレベルでのコンプライアンスを強制するには、CI パイプラインに AI Dev OS チェックを統合してください。

### GitHub Actions の例

```yaml
name: AI Dev OS Compliance
on: [pull_request]

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: ガイドラインコンプライアンスチェック
        run: |
          # オプション1: ガイドラインファイルに markdownlint を実行
          npx markdownlint-cli docs/ai-dev-os/**/*.md

          # オプション2: カスタムコンプライアンススクリプト
          # ai-dev-os-scan の JSON 出力を解析し、違反があれば失敗
          # python scripts/check-compliance.py --strict
```

### 強制レベル

| レベル | 動作 | 推奨対象 |
|--------|------|---------|
| **アドバイザリー** | フックが開発中に警告、CI は違反を報告するがブロックしない | 個人開発者、小規模チーム |
| **警告** | CI が PR に違反コメントを投稿するがマージは許可 | AI Dev OS を段階的に導入中のチーム |
| **ブロッキング** | CI が重大な違反（セキュリティ、認証）で失敗 | 成熟したルールを持つ確立されたチーム |

アドバイザリーレベルから始めて、ルールが成熟するにつれてエスカレートしてください（`[draft]` → `[proven]`）。

---

## 9. トラブルシューティング

### フックが発火しない

- フックが `.claude/settings.json` に記述されているか確認（`hooks/hooks.json` を直接読み込むのではない）
- `jq` がインストールされているか確認（`jq --version`）
- Windows の場合、bash が PATH に含まれているか確認

### スキルが見つからない

- スキルファイルが `.claude/skills/` ディレクトリにあるか確認
- 各スキルに有効な YAML frontmatter を持つ `SKILL.md` が必要
- `ls .claude/skills/` で確認

### 偽陽性の違反

- チェックが厳しすぎる場合、`03_guidelines/` の該当ガイドラインを編集
- 削除前に `/ai-dev-os-why` でルールの根拠を確認
- 削除ではなく緩和を検討

### パフォーマンス

- フックチェックは Write/Edit のたびに実行される — 遅い場合はガイドラインのスコープを縮小
- 重い操作には `context: fork` スキル（extract, audit, evolve）を使用
- サブエージェントは独立コンテキストで実行されるため、メインセッションを汚さない

---

Languages: [English](../../operation-guide.md) | 日本語 | [简体中文](../zh-CN/operation-guide.md) | [한국어](../ko/operation-guide.md) | [Español](../es/operation-guide.md)
