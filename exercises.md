# Claude Code ハンズオン演習問題集

## 演習の進め方

各演習には以下の要素が含まれています：
- **目的**: 何を学ぶか
- **タスク**: 具体的な作業内容
- **ヒント**: 実装のガイダンス
- **発展課題**: さらなる学習のための追加タスク

---

## 演習1: サブエージェントの作成

### 目的
専門分野に特化したサブエージェントを作成し、タスクに応じた最適な応答を得る方法を学びます。

### タスク1-1: バイオインフォマティクス専門エージェント

`.claude/agents/bioinformatics_expert.json` を作成し、以下の要件を満たすエージェントを定義してください：

**要件:**
- メタボロミクス、ゲノミクス、プロテオミクスに精通
- Python (pandas, biopython, scipy) と R (Bioconductor) を使用
- 統計的有意性を常に評価
- 再現可能な解析パイプラインを構築
- 科学論文に適した出力形式

**ヒント:**
```json
{
  "name": "bioinformatics_expert",
  "description": "...",
  "system_prompt": "あなたは... 以下の原則に従ってください：\n1. ...\n2. ...",
  "tools": [...],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": [...]
}
```

**検証:**
作成したエージェントで以下のタスクを実行してください：
```bash
claude code --agent bioinformatics_expert "sample_data.csvを読み込み、t検定を実行してp値を計算"
```

### タスク1-2: DevOpsエンジニア・エージェント

`.claude/agents/devops_engineer.json` を作成してください。

**要件:**
- Kubernetes、Docker、CI/CDに精通
- Infrastructure as Code (Terraform, Ansible) の専門家
- セキュリティとコンプライアンスを重視
- モニタリングとロギングのベストプラクティスを適用

**検証タスク:**
```bash
claude code --agent devops_engineer "Dockerfile を作成してPythonアプリケーションをコンテナ化"
```

### タスク1-3: あなた独自の専門エージェント

あなたの業務や研究に特化したエージェントを作成してください。

**考慮事項:**
- どんな専門知識が必要か？
- どのツールやライブラリを使用するか？
- どんな出力形式が望ましいか？
- どのファイルをコンテキストに含めるべきか？

---

## 演習2: 出力スタイルのカスタマイズ

### 目的
タスクや読者に応じた適切な出力形式を定義する方法を学びます。

### タスク2-1: 学習者向け詳細スタイル

`.claude/output_styles/educational.json` を作成してください。

**要件:**
- 初学者向けの丁寧な説明
- ステップバイステップの解説
- コード例と実行結果の提示
- よくある間違いと対処法
- 関連リソースへのリンク

**サンプル構造:**
```json
{
  "name": "educational",
  "description": "...",
  "format": {
    "include_reasoning": true,
    "step_by_step": true,
    "code_comments": "extensive",
    "include_examples": true,
    "include_common_pitfalls": true
  },
  "sections": {
    "概要": true,
    "前提知識": true,
    "実装手順": true,
    "コード解説": true,
    "実行例": true,
    "よくある間違い": true,
    "発展的内容": true,
    "参考資料": true
  },
  "tone": "friendly_educational",
  "language": "ja"
}
```

**検証:**
```bash
claude code --style educational "再帰関数の概念と実装方法を教えて"
```

### タスク2-2: コードレビュー用スタイル

`.claude/output_styles/code_review.json` を作成してください。

**要件:**
- 構造化されたレビュー形式
- 問題の重要度分類（Critical/Major/Minor）
- 具体的な改善提案
- コード例付き
- ベストプラクティスへの言及

**推奨セクション:**
- サマリー
- アーキテクチャ評価
- コード品質
- セキュリティ
- パフォーマンス
- 保守性
- テスト
- ドキュメンテーション
- 改善提案

### タスク2-3: 研究論文形式スタイル

`.claude/output_styles/research_paper.json` を作成してください。

**要件:**
- 科学論文の構造に準拠
- Abstract、Introduction、Methods、Results、Discussion
- 図表の適切な配置
- 引用形式の統一
- 統計情報の明記

---

## 演習3: カスタムコマンドの作成

### 目的
頻繁に実行するワークフローを自動化するカスタムコマンドを作成します。

### タスク3-1: データ品質チェックコマンド

`.claude/commands/data_commands.json` に以下のコマンドを定義してください：

**コマンド名:** `data-quality-check`

**機能:**
1. CSVファイルを読み込む
2. 欠損値の数と割合を計算
3. 各列のデータ型を確認
4. 数値列の基本統計量（平均、標準偏差、範囲）
5. 重複行の検出
6. 外れ値の検出（IQR法）
7. レポート生成（Markdown形式）

**パラメータ:**
- `input_file`: 入力CSVファイル（必須）
- `output_report`: 出力レポートファイル（デフォルト: quality_report.md）

**テンプレート例:**
```json
{
  "name": "data-quality-check",
  "description": "データ品質チェックを実行",
  "template": "以下のデータ品質チェックを実行:\n\n1. データ読み込み: {{input_file}}\n2. 欠損値分析\n3. データ型検証\n4. 基本統計量計算\n5. 重複チェック\n6. 外れ値検出\n7. レポート生成: {{output_report}}\n\nPythonで実装し、pandasを使用してください。",
  "parameters": [...],
  "agent": "data_scientist",
  "style": "report"
}
```

**検証:**
```bash
claude code data-quality-check --input_file sample_data.csv
```

### タスク3-2: テストスイート実行コマンド

**コマンド名:** `run-tests`

**機能:**
1. ユニットテスト実行
2. カバレッジレポート生成
3. リント（コード品質チェック）
4. 型チェック（TypeScriptまたはPython）
5. 結果サマリー表示

**パラメータ:**
- `test_type`: テストタイプ（unit/integration/e2e）
- `coverage_threshold`: カバレッジ閾値（デフォルト: 80）
- `fail_on_error`: エラー時に失敗させるか（デフォルト: true）

### タスク3-3: 論文図表生成コマンド

**コマンド名:** `generate-figures`

**機能:**
1. データファイルを読み込み
2. 指定された図表タイプを生成
3. 論文品質の高解像度出力（300 DPI以上）
4. 図のキャプションを自動生成
5. LaTeX形式のコード出力

**パラメータ:**
- `data_file`: データファイル
- `figure_type`: 図表タイプ（barplot/lineplot/heatmap/scatter/boxplot）
- `output_format`: 出力形式（png/pdf/svg）
- `style`: プロットスタイル（seaborn/matplotlib）

---

## 演習4: フックの実装

### 目的
カスタムコマンドの前後で実行される処理を実装します。

### タスク4-1: バックアップフック

`.claude/hooks/create_backup.sh` を作成してください。

**機能:**
- コマンド実行前に既存ファイルをバックアップ
- タイムスタンプ付きファイル名
- バックアップディレクトリの作成

```bash
#!/bin/bash
# Pre-hook: ファイルバックアップ

BACKUP_DIR=".claude/backups"
mkdir -p "$BACKUP_DIR"

if [ -f "$OUTPUT_FILE" ]; then
    TIMESTAMP=$(date +%Y%m%d_%H%M%S)
    BACKUP_PATH="$BACKUP_DIR/$(basename $OUTPUT_FILE).backup_$TIMESTAMP"
    cp "$OUTPUT_FILE" "$BACKUP_PATH"
    echo "✓ Backup created: $BACKUP_PATH"
fi
```

### タスク4-2: 検証フック

`.claude/hooks/validate_code.py` を作成してください。

**機能:**
- 生成されたコードの構文チェック
- コードスタイルチェック
- 簡易的なセキュリティスキャン
- 結果をログファイルに出力

```python
#!/usr/bin/env python3
import ast
import sys
import os

def validate_python_code(file_path):
    """Pythonコードの検証"""
    try:
        with open(file_path, 'r') as f:
            code = f.read()
        
        # 構文チェック
        ast.parse(code)
        print(f"✓ Syntax check passed: {file_path}")
        
        # 簡易的なセキュリティチェック
        dangerous_patterns = ['eval(', 'exec(', '__import__']
        for pattern in dangerous_patterns:
            if pattern in code:
                print(f"⚠ Warning: Potentially dangerous pattern found: {pattern}")
        
        return True
    except SyntaxError as e:
        print(f"✗ Syntax error in {file_path}: {e}")
        return False
    except Exception as e:
        print(f"✗ Validation error: {e}")
        return False

if __name__ == "__main__":
    output_file = os.environ.get("OUTPUT_FILE")
    if output_file and output_file.endswith('.py'):
        success = validate_python_code(output_file)
        sys.exit(0 if success else 1)
    else:
        print("Skipping validation for non-Python file")
        sys.exit(0)
```

### タスク4-3: 通知フック

`.claude/hooks/send_notification.sh` を作成してください。

**機能:**
- コマンド完了時に通知を送信
- ステータス（成功/失敗）を含む
- 実行時間の記録

---

## 演習5: 統合プロジェクト

### 目的
これまでの学習内容を統合して、実際のワークフローを構築します。

### プロジェクト: メタボロミクス解析パイプライン

#### フェーズ1: プロジェクト構造の作成

```
metabolomics_project/
├── .claude/
│   ├── config.json
│   ├── agents/
│   │   └── metabolomics_analyst.json
│   ├── output_styles/
│   │   └── scientific_report.json
│   ├── commands/
│   │   └── metabolomics_commands.json
│   └── hooks/
│       ├── validate_data.py
│       └── generate_summary.sh
├── data/
│   ├── raw/
│   └── processed/
├── scripts/
├── results/
└── reports/
```

#### フェーズ2: エージェントの定義

**metabolomics_analyst.json** を作成：
- 専門知識: メタボロミクス、統計解析、パスウェイ解析
- ツール: Python (pandas, scipy, statsmodels), R (xcms, metaboAnalystR)
- 出力: 科学論文品質のレポート

#### フェーズ3: カスタムコマンドの実装

以下のコマンドを実装してください：

1. **preprocess-metabolomics**
   - 生データの品質管理
   - ピーク検出とアライメント
   - 正規化
   - データクリーニング

2. **statistical-analysis**
   - 群間比較（t検定、ANOVA）
   - 多重検定補正（FDR、Bonferroni）
   - PCA、PLS-DA
   - ヒートマップ生成

3. **pathway-analysis**
   - KEGG pathway enrichment
   - Reactome pathway analysis
   - 代謝ネットワーク構築
   - 可視化

4. **generate-report**
   - 全解析結果の統合
   - 図表の配置
   - 統計サマリー
   - PDF出力

#### フェーズ4: ワークフロー実行

完全な解析パイプラインを実行：

```bash
# データ前処理
claude code preprocess-metabolomics \
  --input data/raw/metabolites.csv \
  --output data/processed/normalized.csv

# 統計解析
claude code statistical-analysis \
  --input data/processed/normalized.csv \
  --groups "control,treatment" \
  --method "t-test"

# パスウェイ解析
claude code pathway-analysis \
  --input results/significant_metabolites.csv \
  --database KEGG

# レポート生成
claude code generate-report \
  --project metabolomics_project \
  --output reports/final_report.pdf
```

---

## 演習6: チーム設定の標準化

### 目的
チーム全体で共有可能な設定を作成します。

### タスク6-1: 共有設定ファイルの作成

`.claude/team_config.json`:

```json
{
  "project_name": "Research Project",
  "team": {
    "coding_standards": "PEP8",
    "documentation_style": "Google",
    "commit_message_format": "conventional"
  },
  "shared_agents": [
    "data_scientist",
    "code_reviewer",
    "documentation_writer"
  ],
  "required_styles": [
    "production",
    "report"
  ],
  "mandatory_hooks": [
    "backup_existing",
    "validate_output"
  ]
}
```

### タスク6-2: ドキュメンテーション作成

チーム向けのREADMEを作成：
- 利用可能なエージェントの説明
- カスタムコマンドの使用方法
- ベストプラクティス
- トラブルシューティング

---

## 演習7: CI/CD統合

### 目的
Claude CodeをCI/CDパイプラインに統合します。

### タスク7-1: GitHub Actions ワークフロー

`.github/workflows/claude_code_check.yml`:

```yaml
name: Claude Code Quality Check

on: [pull_request]

jobs:
  code-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Claude Code
        run: |
          # Claude Code のセットアップ
          
      - name: Run Code Review
        run: |
          claude code --agent code_reviewer \
            --style production \
            "変更されたファイルをレビュー"
      
      - name: Run Tests
        run: |
          claude code run-tests \
            --test_type unit \
            --coverage_threshold 80
```

---

## 付録: チートシート

### サブエージェント作成

```json
{
  "name": "agent_name",
  "description": "説明",
  "system_prompt": "エージェントの指示",
  "tools": ["bash_tool", "create_file", ...],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": ["pattern1", "pattern2"]
}
```

### 出力スタイル作成

```json
{
  "name": "style_name",
  "description": "説明",
  "format": {
    "include_reasoning": true/false,
    "code_comments": "minimal/moderate/extensive"
  },
  "sections": {
    "section_name": true/false
  },
  "tone": "direct/friendly/professional",
  "language": "ja"
}
```

### カスタムコマンド作成

```json
{
  "name": "command-name",
  "description": "説明",
  "template": "実行内容: {{param1}} {{param2}}",
  "parameters": [
    {
      "name": "param1",
      "type": "string/file/choice",
      "required": true/false,
      "description": "説明"
    }
  ],
  "agent": "agent_name",
  "style": "style_name"
}
```

### よく使うコマンド

```bash
# エージェント指定
claude code --agent agent_name "タスク"

# スタイル指定
claude code --style style_name "タスク"

# 両方指定
claude code --agent agent_name --style style_name "タスク"

# カスタムコマンド実行
claude code command-name --param1 value1 --param2 value2

# 設定確認
claude code --list-agents
claude code --list-styles
claude code --list-commands
```

---

## 解答例とフィードバック

各演習の解答例は `solutions/` ディレクトリに用意されています。
自分の実装と比較して、改善点を見つけましょう。

---

**Happy Coding with Claude Code! 🚀**
