# Claude Code ハンズオン - Quick Start ガイド

このガイドでは、最短でClaude Codeの高度な機能を試せるように、すぐに使えるサンプルを提供します。

## 10分でできる基本セットアップ

### ステップ1: プロジェクトディレクトリ作成（1分）

```bash
mkdir -p my-claude-project/.claude/{agents,output_styles,commands,hooks}
cd my-claude-project
```

### ステップ2: 最初のサブエージェントを作成（3分）

`.claude/agents/quick_coder.json`:

```bash
cat > .claude/agents/quick_coder.json << 'EOF'
{
  "name": "quick_coder",
  "description": "素早いコード生成の専門家",
  "system_prompt": "あなたは効率的なコード生成の専門家です。簡潔で実用的なコードを書きます。",
  "tools": ["bash_tool", "create_file", "str_replace", "view"],
  "default_model": "claude-sonnet-4-5-20250929"
}
EOF
```

**すぐに試す:**
```bash
claude code --agent quick_coder "Hello Worldを10個の異なる言語で書いて"
```

### ステップ3: 簡潔な出力スタイル作成（2分）

`.claude/output_styles/quick.json`:

```bash
cat > .claude/output_styles/quick.json << 'EOF'
{
  "name": "quick",
  "description": "要点だけの簡潔な出力",
  "format": {
    "max_explanation_length": 200,
    "code_comments": "minimal",
    "include_reasoning": false
  },
  "tone": "direct",
  "language": "ja"
}
EOF
```

**すぐに試す:**
```bash
claude code --style quick "Pythonでフィボナッチ数列を計算する関数"
```

### ステップ4: 最初のカスタムコマンド作成（4分）

`.claude/commands/quick_commands.json`:

```bash
cat > .claude/commands/quick_commands.json << 'EOF'
{
  "commands": [
    {
      "name": "quick-script",
      "description": "クイックスクリプト生成",
      "template": "以下のタスクを実行するシンプルなスクリプトを作成:\n{{task}}\n\n言語: {{language}}\nファイル名: {{filename}}",
      "parameters": [
        {
          "name": "task",
          "type": "string",
          "required": true,
          "description": "実行したいタスク"
        },
        {
          "name": "language",
          "type": "choice",
          "choices": ["python", "bash", "javascript", "ruby"],
          "required": false,
          "default": "python",
          "description": "プログラミング言語"
        },
        {
          "name": "filename",
          "type": "string",
          "required": false,
          "default": "script",
          "description": "出力ファイル名"
        }
      ],
      "agent": "quick_coder",
      "style": "quick"
    }
  ]
}
EOF
```

**すぐに試す:**
```bash
claude code quick-script \
  --task "ディレクトリ内のすべてのPNGファイルをリサイズ" \
  --language python \
  --filename resize_images.py
```

## 実践的なサンプル集

### サンプル1: データ分析エージェント（即使用可）

```bash
cat > .claude/agents/data_analyst.json << 'EOF'
{
  "name": "data_analyst",
  "description": "データ分析の専門家",
  "system_prompt": "データ分析のエキスパートとして、以下を実行:\n1. データの探索的分析\n2. 可視化の作成\n3. 統計的洞察の提供\n\nPandas, NumPy, Matplotlibを使用してください。",
  "tools": ["bash_tool", "create_file", "view"],
  "default_model": "claude-sonnet-4-5-20250929",
  "context_files": ["data/**/*.csv", "*.ipynb"]
}
EOF
```

**使用例:**
```bash
# サンプルデータを作成
cat > data.csv << 'EOF'
date,sales,visitors
2024-01-01,1200,450
2024-01-02,1350,520
2024-01-03,980,380
2024-01-04,1580,670
2024-01-05,1420,590
EOF

# 分析を実行
claude code --agent data_analyst "data.csvを分析して売上トレンドをグラフ化"
```

### サンプル2: デバッグ専門エージェント

```bash
cat > .claude/agents/debugger.json << 'EOF'
{
  "name": "debugger",
  "description": "デバッグとエラー解決の専門家",
  "system_prompt": "バグ修正の専門家として:\n1. エラーの原因を特定\n2. 修正方法を提案\n3. テストケースを作成\n4. 予防策を提示\n\nわかりやすく段階的に説明してください。",
  "tools": ["bash_tool", "str_replace", "view"],
  "default_model": "claude-sonnet-4-5-20250929"
}
EOF
```

**使用例:**
```bash
claude code --agent debugger "このPythonスクリプトのエラーを修正: error_script.py"
```

### サンプル3: 即使用可能なカスタムコマンドセット

```bash
cat > .claude/commands/essential_commands.json << 'EOF'
{
  "commands": [
    {
      "name": "readme",
      "description": "READMEファイル生成",
      "template": "プロジェクトのREADME.mdを作成:\n\nプロジェクト名: {{project_name}}\n説明: {{description}}\n\n以下を含める:\n- プロジェクト概要\n- インストール手順\n- 使用方法\n- 貢献方法\n- ライセンス情報",
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
        }
      ],
      "style": "detailed"
    },
    {
      "name": "test-gen",
      "description": "テストコード生成",
      "template": "{{file}}のユニットテストを作成:\n\n- すべての関数/メソッドをカバー\n- エッジケースを含む\n- pytest形式で出力\n\nテストファイル: test_{{file}}",
      "parameters": [
        {
          "name": "file",
          "type": "file",
          "required": true,
          "description": "テスト対象ファイル"
        }
      ],
      "agent": "quick_coder"
    },
    {
      "name": "refactor",
      "description": "コードリファクタリング",
      "template": "{{file}}をリファクタリング:\n\n改善点:\n- 可読性の向上\n- パフォーマンスの最適化\n- コードの重複削除\n- ベストプラクティスの適用\n\n元のファイルは保持し、新しいバージョンを {{file}}.refactored として保存",
      "parameters": [
        {
          "name": "file",
          "type": "file",
          "required": true
        }
      ],
      "agent": "quick_coder",
      "pre_hooks": ["backup_existing"]
    }
  ]
}
EOF
```

**使用例:**

```bash
# README生成
claude code readme \
  --project_name "MyAwesomeProject" \
  --description "素晴らしいデータ分析ツール"

# テスト生成
claude code test-gen --file my_module.py

# リファクタリング
claude code refactor --file legacy_code.py
```

## 実用的なワークフロー例

### ワークフロー1: 新規プロジェクト立ち上げ（5分）

```bash
# 1. プロジェクト構造作成
mkdir -p my-project/{src,tests,docs,data}
cd my-project

# 2. README生成
claude code readme \
  --project_name "My Project" \
  --description "データ処理パイプライン"

# 3. メインスクリプト生成
claude code quick-script \
  --task "CSVファイルを読み込んで集計処理" \
  --language python \
  --filename src/main.py

# 4. テスト生成
claude code test-gen --file src/main.py

# 5. ドキュメント生成
claude code --agent quick_coder "使用方法のドキュメントを docs/usage.md に作成"
```

### ワークフロー2: データ分析レポート作成（10分）

```bash
# 1. データ分析エージェントで探索的分析
claude code --agent data_analyst \
  "data/sales.csv の基本統計とトレンド分析"

# 2. 可視化作成
claude code --agent data_analyst \
  "月別売上の棒グラフとトレンドラインを作成"

# 3. レポート生成
claude code --style report \
  --agent data_analyst \
  "分析結果を統合してレポート作成: reports/monthly_analysis.md"
```

### ワークフロー3: コードレビューと改善（15分）

```bash
# 1. コードレビュー
claude code --agent debugger "src/ ディレクトリ全体をレビュー"

# 2. 問題箇所の修正
claude code --agent debugger "指摘された問題を修正"

# 3. テスト追加
claude code test-gen --file src/fixed_module.py

# 4. ドキュメント更新
claude code --agent quick_coder "変更内容を CHANGELOG.md に記録"
```

## トラブルシューティング

### 問題1: エージェントが見つからない

```bash
# エージェントリストを確認
claude code --list-agents

# エージェントファイルのパスを確認
ls -la .claude/agents/
```

### 問題2: カスタムコマンドが実行できない

```bash
# コマンドリストを確認
claude code --list-commands

# JSONファイルの構文チェック
python3 -m json.tool .claude/commands/custom_commands.json
```

### 問題3: スタイルが適用されない

```bash
# スタイルリストを確認
claude code --list-styles

# スタイルファイルの検証
cat .claude/output_styles/your_style.json | python3 -m json.tool
```

## 次のステップ

1. **メインハンズオン資料**: `claude_code_handson.md` で詳細を学習
2. **演習問題**: `exercises.md` で実践的なスキルを習得
3. **カスタマイズ**: 自分のワークフローに合わせて設定をカスタマイズ

## 便利なエイリアス設定

`.claude/config.json` に追加:

```json
{
  "aliases": {
    "qa": "data-quality-check",
    "review": "code-review",
    "doc": "generate-docs",
    "test": "test-gen"
  },
  "default_agent": "quick_coder",
  "default_style": "quick"
}
```

これで以下のように短縮できます:

```bash
claude code qa --input data.csv
claude code review --file my_code.py
claude code doc --project "MyProject"
claude code test --file module.py
```

## よく使うコマンドまとめ

```bash
# エージェント使用
claude code --agent <agent_name> "<task>"

# スタイル指定
claude code --style <style_name> "<task>"

# 組み合わせ
claude code --agent <agent> --style <style> "<task>"

# カスタムコマンド
claude code <command-name> --param value

# 情報確認
claude code --list-agents
claude code --list-styles
claude code --list-commands
```

---

**5分で始めるClaude Code - すぐに生産性向上! 🚀**
