# Claude Code ハンズオン資料

## 目次
1. [はじめに](#はじめに)
2. [サブエージェントの作成](#サブエージェントの作成)
3. [出力スタイルのカスタマイズ](#出力スタイルのカスタマイズ)
4. [カスタムコマンドの作成](#カスタムコマンドの作成)
5. [実践演習](#実践演習)

---

## はじめに

このハンズオンでは、Claude Codeの高度な機能を学びます：
- **サブエージェント**: 特定のタスクに特化したエージェントの作成
- **出力スタイル**: Claude Codeの応答形式のカスタマイズ
- **カスタムコマンド**: 頻繁に使用する操作の自動化

### 前提条件
- Claude Codeがインストール済み
- 基本的なコマンドライン操作の知識
- テキストエディタの使用経験

---

## サブエージェントの作成

### サブエージェントとは？

サブエージェントは、特定のタスクや役割に特化したClaude Codeの設定プロファイルです。例えば：
- **データサイエンティスト**: データ分析やビジュアライゼーションに特化
- **フロントエンド開発者**: React/Vue.jsなどのUIコンポーネント作成に特化
- **コードレビュアー**: コード品質チェックとベストプラクティス提案に特化

### サブエージェントの作成手順

#### 1. サブエージェント設定ファイルの作成

サブエージェントは `.claude/` ディレクトリに JSON 形式で保存します。

```bash
mkdir -p .claude/agents
```

#### 2. データサイエンティスト・エージェントの例

`.claude/agents/data_scientist.json` を作成：

```json
{
  "name": "data_scientist",
  "description": "データ分析とビジュアライゼーションの専門家",
  "system_prompt": "あなたは経験豊富なデータサイエンティストです。以下の原則に従ってください：\n\n1. データ分析では常に探索的データ分析(EDA)から始める\n2. 可視化にはmatplotlib、seaborn、plotlyを適切に使い分ける\n3. 統計的な根拠を示し、仮説検定の結果を明確に説明する\n4. コードには適切なコメントとドキュメンテーションを含める\n5. 再現性を重視し、乱数シードを設定する\n\nPythonを使用し、pandas、numpy、scikit-learnなどの標準ライブラリを活用してください。",
  "tools": ["bash_tool", "create_file", "str_replace", "view"],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": [
    "data/**/*.csv",
    "notebooks/**/*.ipynb",
    "requirements.txt"
  ]
}
```

#### 3. フロントエンド開発者エージェントの例

`.claude/agents/frontend_dev.json` を作成：

```json
{
  "name": "frontend_dev",
  "description": "モダンフロントエンド開発の専門家",
  "system_prompt": "あなたはReact、TypeScript、Tailwind CSSに精通したフロントエンド開発者です。\n\n開発原則：\n1. コンポーネントは再利用可能で、単一責任の原則に従う\n2. TypeScriptで型安全性を確保する\n3. アクセシビリティ（a11y）を常に考慮する\n4. レスポンシブデザインをモバイルファーストで実装\n5. パフォーマンスを最適化（遅延読み込み、メモ化など）\n\nコードは簡潔で読みやすく、ESLintとPrettierの規約に従ってください。",
  "tools": ["bash_tool", "create_file", "str_replace", "view"],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": [
    "src/**/*.tsx",
    "src/**/*.ts",
    "package.json",
    "tsconfig.json",
    "tailwind.config.js"
  ]
}
```

#### 4. コードレビュアー・エージェントの例

`.claude/agents/code_reviewer.json` を作成：

```json
{
  "name": "code_reviewer",
  "description": "コード品質とベストプラクティスの専門家",
  "system_prompt": "あなたは経験豊富なコードレビュアーです。レビュー時には以下を確認してください：\n\n1. コードの可読性と保守性\n2. パフォーマンスの問題や最適化の機会\n3. セキュリティの脆弱性\n4. エラーハンドリングの適切性\n5. テストカバレッジ\n6. ドキュメンテーションの充実度\n\nフィードバックは建設的で具体的に。改善案を提示し、重要度（Critical/Major/Minor）を明記してください。",
  "tools": ["bash_tool", "view"],
  "default_model": "claude-sonnet-4-5-20250929",
  "output_style": {
    "format": "structured",
    "sections": ["summary", "issues", "recommendations"]
  }
}
```

### サブエージェントの使用方法

#### コマンドラインから使用

```bash
# データサイエンティスト・エージェントを使用
claude code --agent data_scientist "CSVファイルを読み込んで基本統計と相関分析を実行"

# フロントエンド開発者エージェントを使用
claude code --agent frontend_dev "ログインフォームコンポーネントをTailwind CSSで作成"

# コードレビュアー・エージェントを使用
claude code --agent code_reviewer "src/utils/auth.tsをレビューしてください"
```

#### プロジェクト設定でデフォルト指定

`.claude/config.json`:

```json
{
  "default_agent": "data_scientist",
  "agents_directory": ".claude/agents"
}
```

### 演習1: 自分のサブエージェントを作成

**タスク**: あなたの専門分野に特化したサブエージェントを作成してください。

例：
- バイオインフォマティクス専門家
- DevOpsエンジニア
- テクニカルライター
- API設計者

---

## 出力スタイルのカスタマイズ

### 出力スタイルとは？

Claude Codeの応答形式を制御し、プロジェクトや用途に応じた最適な出力を得るための設定です。

### スタイル設定の種類

#### 1. フォーマット設定

`.claude/output_styles/concise.json`:

```json
{
  "name": "concise",
  "description": "簡潔で要点を絞った出力",
  "format": {
    "max_explanation_length": 500,
    "code_comments": "minimal",
    "include_reasoning": false,
    "prefer_inline_code": true
  },
  "tone": "direct",
  "language": "ja"
}
```

#### 2. 詳細説明スタイル

`.claude/output_styles/detailed.json`:

```json
{
  "name": "detailed",
  "description": "教育的で詳しい説明付き出力",
  "format": {
    "include_reasoning": true,
    "step_by_step": true,
    "code_comments": "extensive",
    "include_examples": true,
    "include_alternatives": true
  },
  "sections": {
    "overview": true,
    "implementation": true,
    "explanation": true,
    "testing": true,
    "next_steps": true
  },
  "tone": "educational",
  "language": "ja"
}
```

#### 3. プロダクション用スタイル

`.claude/output_styles/production.json`:

```json
{
  "name": "production",
  "description": "プロダクション品質のコード生成",
  "format": {
    "code_style": "strict",
    "include_error_handling": true,
    "include_logging": true,
    "include_tests": true,
    "documentation": "comprehensive",
    "type_hints": true
  },
  "quality_checks": {
    "security_review": true,
    "performance_check": true,
    "compatibility_check": true
  },
  "tone": "professional",
  "language": "ja"
}
```

#### 4. レポートスタイル

`.claude/output_styles/report.json`:

```json
{
  "name": "report",
  "description": "分析レポート形式の出力",
  "format": {
    "structure": "markdown",
    "include_toc": true,
    "include_summary": true,
    "include_visualizations": true
  },
  "sections": {
    "executive_summary": true,
    "methodology": true,
    "findings": true,
    "recommendations": true,
    "appendix": true
  },
  "output_format": "markdown",
  "language": "ja"
}
```

### スタイルの使用方法

#### コマンドラインから使用

```bash
# 簡潔なスタイルで実行
claude code --style concise "データを読み込んでグラフを作成"

# 詳細な説明付きで実行
claude code --style detailed "機械学習モデルを実装"

# プロダクション品質で実行
claude code --style production "認証APIエンドポイントを作成"
```

#### エージェントとスタイルの組み合わせ

```bash
# データサイエンティスト + レポートスタイル
claude code --agent data_scientist --style report "売上データの分析レポートを作成"
```

### プロジェクト固有の設定

`.claude/config.json`:

```json
{
  "default_style": "detailed",
  "styles": {
    "data_analysis": "report",
    "quick_fix": "concise",
    "new_feature": "production"
  },
  "auto_select_style": true
}
```

### 演習2: カスタム出力スタイルの作成

**タスク**: あなたの作業に適した出力スタイルを作成してください。

考慮事項：
- 説明の詳細度
- コードコメントの量
- 含めるべきセクション
- 言語設定

---

## カスタムコマンドの作成

### カスタムコマンドとは？

頻繁に実行するタスクを自動化するための独自コマンドです。

### コマンド定義ファイルの作成

#### 1. 基本構造

`.claude/commands/custom_commands.json`:

```json
{
  "commands": [
    {
      "name": "analyze-csv",
      "description": "CSVファイルの基本分析を実行",
      "template": "以下のタスクを実行してください：\n1. {{file}}を読み込む\n2. 基本統計量を計算\n3. 欠損値を確認\n4. 相関行列を作成\n5. 主要な列のヒストグラムを作成\n\n出力: analysis_report.md",
      "parameters": [
        {
          "name": "file",
          "type": "file",
          "required": true,
          "description": "分析するCSVファイル"
        }
      ],
      "agent": "data_scientist",
      "style": "report"
    }
  ]
}
```

#### 2. データ処理パイプラインコマンド

```json
{
  "name": "etl-pipeline",
  "description": "ETLパイプラインを実行",
  "template": "データETLパイプラインを実行:\n\n1. Extract: {{source}}からデータ抽出\n2. Transform:\n   - データクリーニング\n   - {{transformations}}を適用\n   - データ検証\n3. Load: {{destination}}に保存\n\nログファイル: etl_log_{{timestamp}}.txt",
  "parameters": [
    {
      "name": "source",
      "type": "string",
      "required": true,
      "description": "データソース（ファイルパスまたはURL）"
    },
    {
      "name": "transformations",
      "type": "string",
      "required": false,
      "default": "標準的なクリーニング",
      "description": "適用する変換処理"
    },
    {
      "name": "destination",
      "type": "string",
      "required": true,
      "description": "出力先"
    }
  ],
  "agent": "data_scientist",
  "pre_hooks": ["backup_existing"],
  "post_hooks": ["validate_output", "send_notification"]
}
```

#### 3. コンポーネント生成コマンド

```json
{
  "name": "create-component",
  "description": "Reactコンポーネントを生成",
  "template": "以下の仕様でReactコンポーネントを作成:\n\nコンポーネント名: {{name}}\nタイプ: {{type}}\n機能: {{features}}\n\nファイル構成:\n- src/components/{{name}}/{{name}}.tsx (メインコンポーネント)\n- src/components/{{name}}/{{name}}.test.tsx (テスト)\n- src/components/{{name}}/{{name}}.stories.tsx (Storybook)\n- src/components/{{name}}/index.ts (エクスポート)\n\nTypeScript, Tailwind CSS, アクセシビリティ対応で実装してください。",
  "parameters": [
    {
      "name": "name",
      "type": "string",
      "required": true,
      "description": "コンポーネント名（PascalCase）"
    },
    {
      "name": "type",
      "type": "choice",
      "choices": ["functional", "form", "layout", "display"],
      "required": true,
      "description": "コンポーネントタイプ"
    },
    {
      "name": "features",
      "type": "string",
      "required": true,
      "description": "実装する機能の説明"
    }
  ],
  "agent": "frontend_dev",
  "style": "production"
}
```

#### 4. ドキュメント生成コマンド

```json
{
  "name": "generate-docs",
  "description": "プロジェクトドキュメントを生成",
  "template": "以下のドキュメントを生成:\n\n1. README.md - プロジェクト概要\n2. API.md - API仕様\n3. CONTRIBUTING.md - 貢献ガイドライン\n4. CHANGELOG.md - 変更履歴\n\nプロジェクト情報:\n- 名前: {{project_name}}\n- 説明: {{description}}\n- 技術スタック: {{tech_stack}}\n\n既存のコードとコメントから情報を抽出し、包括的なドキュメントを作成してください。",
  "parameters": [
    {
      "name": "project_name",
      "type": "string",
      "required": true
    },
    {
      "name": "description",
      "type": "string",
      "required": true
    },
    {
      "name": "tech_stack",
      "type": "string",
      "required": true
    }
  ],
  "style": "detailed"
}
```

### カスタムコマンドの使用

#### 基本的な使用方法

```bash
# CSV分析コマンド
claude code analyze-csv --file data/sales.csv

# ETLパイプライン
claude code etl-pipeline --source data/raw.csv --destination data/processed.csv

# コンポーネント生成
claude code create-component --name Button --type functional --features "クリック可能なボタン、サイズとカラーバリエーション"

# ドキュメント生成
claude code generate-docs --project_name "MyProject" --description "データ分析ツール" --tech_stack "Python, Pandas, FastAPI"
```

#### コマンドのエイリアス設定

`.claude/config.json`:

```json
{
  "aliases": {
    "analyze": "analyze-csv",
    "comp": "create-component",
    "docs": "generate-docs"
  }
}
```

使用例：
```bash
claude code analyze --file data.csv
claude code comp --name Header --type layout --features "ナビゲーション付きヘッダー"
```

### フックの実装

カスタムコマンドの前後で実行するスクリプト：

#### Pre-hook例: `.claude/hooks/backup_existing.sh`

```bash
#!/bin/bash
# 既存ファイルのバックアップ

if [ -f "$OUTPUT_FILE" ]; then
    TIMESTAMP=$(date +%Y%m%d_%H%M%S)
    cp "$OUTPUT_FILE" "${OUTPUT_FILE}.backup_${TIMESTAMP}"
    echo "Backup created: ${OUTPUT_FILE}.backup_${TIMESTAMP}"
fi
```

#### Post-hook例: `.claude/hooks/validate_output.py`

```python
#!/usr/bin/env python3
import sys
import os

def validate_output(file_path):
    """出力ファイルの検証"""
    if not os.path.exists(file_path):
        print(f"Error: Output file not found: {file_path}")
        return False
    
    if os.path.getsize(file_path) == 0:
        print(f"Warning: Output file is empty: {file_path}")
        return False
    
    print(f"Validation passed: {file_path}")
    return True

if __name__ == "__main__":
    output_file = os.environ.get("OUTPUT_FILE")
    success = validate_output(output_file)
    sys.exit(0 if success else 1)
```

### 演習3: カスタムコマンドの作成

**タスク**: 自分の作業フローに役立つカスタムコマンドを作成してください。

アイデア：
- データ前処理パイプライン
- テストスイート実行
- デプロイメント自動化
- レポート生成

---

## 実践演習

### プロジェクト: バイオインフォマティクス分析ワークフロー

以下の要素を組み合わせた実践的なワークフローを構築します。

#### 1. サブエージェント作成

`.claude/agents/bioinformatics.json`:

```json
{
  "name": "bioinformatics",
  "description": "バイオインフォマティクス解析の専門家",
  "system_prompt": "あなたは経験豊富なバイオインフォマティクス研究者です。\n\n専門分野：\n- メタボロミクスデータ解析\n- ネットワーク解析\n- 統計解析とデータビジュアライゼーション\n- パスウェイ解析\n\nツール：\n- Python (pandas, numpy, scipy, networkx)\n- R (Bioconductor packages)\n- データ標準化（mzTab-M）\n\n常に再現性と科学的厳密性を重視してください。",
  "tools": ["bash_tool", "create_file", "str_replace", "view"],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": [
    "data/**/*.csv",
    "data/**/*.mzTab",
    "scripts/**/*.py",
    "notebooks/**/*.ipynb"
  ]
}
```

#### 2. 出力スタイル作成

`.claude/output_styles/scientific_report.json`:

```json
{
  "name": "scientific_report",
  "description": "科学論文形式のレポート",
  "format": {
    "structure": "markdown",
    "include_toc": true,
    "include_methods": true,
    "include_references": true
  },
  "sections": {
    "abstract": true,
    "introduction": true,
    "methods": true,
    "results": true,
    "discussion": true,
    "figures": true,
    "tables": true
  },
  "citation_style": "APA",
  "language": "ja"
}
```

#### 3. カスタムコマンド作成

`.claude/commands/bio_commands.json`:

```json
{
  "commands": [
    {
      "name": "metabolomics-pipeline",
      "description": "メタボロミクスデータ解析パイプライン",
      "template": "メタボロミクスデータ解析を実行:\n\n1. データ読み込み: {{input_file}}\n2. 品質管理:\n   - 欠損値処理\n   - 外れ値検出\n   - 正規化\n3. 統計解析:\n   - {{analysis_type}}\n   - FDR補正\n4. ビジュアライゼーション:\n   - PCAプロット\n   - ヒートマップ\n   - ボルケーノプロット\n5. パスウェイ解析\n6. レポート生成: metabolomics_report.md",
      "parameters": [
        {
          "name": "input_file",
          "type": "file",
          "required": true,
          "description": "入力データファイル"
        },
        {
          "name": "analysis_type",
          "type": "choice",
          "choices": ["t-test", "ANOVA", "regression"],
          "required": true,
          "description": "統計解析手法"
        }
      ],
      "agent": "bioinformatics",
      "style": "scientific_report"
    },
    {
      "name": "network-analysis",
      "description": "生物学的ネットワーク解析",
      "template": "ネットワーク解析を実行:\n\n1. ネットワーク構築: {{network_file}}\n2. トポロジー解析:\n   - 次数分布\n   - クラスタリング係数\n   - 中心性指標\n3. モジュール検出\n4. 可視化: Cytoscape形式出力\n5. 結果レポート: network_analysis_report.md",
      "parameters": [
        {
          "name": "network_file",
          "type": "file",
          "required": true,
          "description": "ネットワークデータ（エッジリスト）"
        }
      ],
      "agent": "bioinformatics",
      "style": "scientific_report"
    }
  ]
}
```

### 実行例

```bash
# メタボロミクスパイプライン実行
claude code metabolomics-pipeline \
  --input_file data/metabolites.csv \
  --analysis_type t-test

# ネットワーク解析
claude code network-analysis \
  --network_file data/protein_interactions.txt

# エージェントとスタイルを直接指定
claude code --agent bioinformatics --style scientific_report \
  "KEGG pathwayデータを取得してネットワークグラフを作成"
```

---

## まとめ

このハンズオンで学んだこと：

1. **サブエージェント**: タスク特化型のエージェント作成
2. **出力スタイル**: 用途に応じた出力形式のカスタマイズ
3. **カスタムコマンド**: ワークフロー自動化

### 次のステップ

- 実際のプロジェクトでサブエージェントを活用
- チームで共有可能な設定の標準化
- CI/CDパイプラインへの統合
- より高度なフックスクリプトの実装

### リソース

- Claude Code公式ドキュメント: https://docs.claude.com
- コミュニティ事例集
- テンプレートリポジトリ

---

## 付録: 設定ファイル一覧

```
project/
├── .claude/
│   ├── config.json              # 全体設定
│   ├── agents/                  # サブエージェント定義
│   │   ├── data_scientist.json
│   │   ├── frontend_dev.json
│   │   ├── code_reviewer.json
│   │   └── bioinformatics.json
│   ├── output_styles/           # 出力スタイル定義
│   │   ├── concise.json
│   │   ├── detailed.json
│   │   ├── production.json
│   │   ├── report.json
│   │   └── scientific_report.json
│   ├── commands/                # カスタムコマンド定義
│   │   ├── custom_commands.json
│   │   └── bio_commands.json
│   └── hooks/                   # フックスクリプト
│       ├── backup_existing.sh
│       ├── validate_output.py
│       └── send_notification.sh
```

---

**ハンズオン作成日**: 2025年
**対象バージョン**: Claude Code latest
