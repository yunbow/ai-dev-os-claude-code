# AI Dev OS Plugin — Claude Code への貢献

貢献に興味を持っていただきありがとうございます！

## 貢献方法

### 問題の報告

- [GitHub Issues](https://github.com/yunbow/ai-dev-os-plugin-claude-code/issues) をご利用ください
- 影響を受けるスキル、フック、またはエージェントを明記してください

### プルリクエスト

1. リポジトリをフォークする
2. フィーチャーブランチを作成する
3. 変更を加える
4. わかりやすいメッセージでコミットする
5. プルリクエストを開く

### 貢献できる内容

| ディレクトリ | 求めている貢献 | ガイドライン |
|-----------|-------------|------------|
| `skills/` | スキルの改善、新規スキル | SKILL.md フォーマット（YAML フロントマター + 実行フロー）に従ってください。スキルは1つのタスクに集中させてください。 |
| `agents/` | エージェントの改善 | エージェント .md フォーマット（YAML フロントマター + 役割の説明）に従ってください。 |
| `hooks/` | フックの改善、新しいイベントハンドラー | 徹底的にテストしてください — フックはすべてのツール呼び出し時に実行されます。誤検知を避けてください。 |
| `templates/` | テンプレートの改善 | CLAUDE.md テンプレートは Two-Tier Context Strategy（10-15 個の重要なルールのみ）に従ってください。 |
| `checklist-templates/` | 新しい言語のチェックリスト、改善 | チェックリストは実行可能かつ検証可能なものにしてください。 |

### スキル開発ガイドライン

- 各スキルは `skills/{name}/SKILL.md` に配置します
- 必須フロントマター: `name`, `description`, `allowed-tools`
- 任意: `argument-hint`, `context`, `agent`
- 実行フローは明確な判断ポイントを含むステップバイステップにしてください
- 出力形式は一貫したマークダウンテーブルを使用してください

### フック開発ガイドライン

- フックは `hooks/hooks.json` で定義されています
- ソースファイル以外（node_modules, dist, 画像など）をフィルタリングしてください
- `additionalContext` は提案用に使用し、ブロッキングには使用しないでください
- TypeScript と Python の両方のプロジェクトでテストしてください

### クロスプラグイン機能の同等性

このプラグインに新機能を追加する際は、以下への同等機能の追加も検討してください：

- [ai-dev-os-plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro)（ステアリングルールとして）
- [ai-dev-os-plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor)（.mdc ルールとして）

### 翻訳ガイド

- 翻訳は `docs/i18n/{lang}/` にあります
- 現在サポートされている言語: `ja`, `zh-CN`, `ko`, `es`

## 行動規範

敬意を持ち、建設的で、包括的であること。

---

Languages: [English](../../../CONTRIBUTING.md) | 日本語
