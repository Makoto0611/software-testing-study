# ソフトウェアテスト技術学習プロジェクト

## 📁 ディレクトリ構成

```
testing/
├── 01_test_fundamentals/          # テスト基礎
│   ├── principles_and_concepts/   # テストの原則と概念
│   └── test_process/             # テストプロセス
├── 02_test_techniques/           # テスト技法
│   ├── static_testing/           # 静的テスト
│   │   ├── review/              # レビュー
│   │   └── static_analysis/     # 静的解析
│   └── dynamic_testing/          # 動的テスト
│       ├── blackbox/            # ブラックボックステスト
│       │   ├── equivalence_partitioning/  # 同値分割
│       │   ├── boundary_value_analysis/   # 境界値分析
│       │   └── decision_table/            # デシジョンテーブル
│       ├── whitebox/            # ホワイトボックステスト
│       └── experience_based/    # 経験ベーステスト
├── 03_specialized_testing/       # 専門領域テスト
│   ├── performance_testing/      # パフォーマンステスト
│   ├── security_testing/         # セキュリティテスト
│   └── mobile_api_db_cloud/     # モバイル/API/DB/クラウド
├── 04_test_automation/          # テスト自動化
│   ├── roi_analysis/            # ROI分析
│   ├── frameworks/              # フレームワーク
│   └── cicd_integration/        # CI/CD統合
├── 05_test_management/          # テスト管理
│   ├── planning/                # 計画
│   ├── execution_management/    # 実行管理
│   └── metrics/                 # メトリクス
├── 06_latest_trends/            # 最新トレンド
│   ├── ai_testing/              # AIテスト
│   ├── big_data/                # ビッグデータ
│   └── iot/                     # IoT
├── exercises/                   # 演習問題
├── progress/                    # 学習進捗管理
└── materials/                   # 教材・資料
```

## 🎯 推奨学習順序

### Phase 1: 基礎固め
1. **テスト基礎** (`01_test_fundamentals/`)
   - テストの原則と概念
   - テストプロセス

### Phase 2: テスト技法習得
2. **ブラックボックステスト** (`02_test_techniques/dynamic_testing/blackbox/`)
   - 同値分割 ✅
   - 境界値分析 ✅
   - **デシジョンテーブル** ← 次回学習予定
3. **静的テスト** (`02_test_techniques/static_testing/`)
4. **ホワイトボックステスト** (`02_test_techniques/dynamic_testing/whitebox/`)
5. **経験ベーステスト** (`02_test_techniques/dynamic_testing/experience_based/`)

### Phase 3: 専門分野
6. **専門領域テスト** (`03_specialized_testing/`)
7. **テスト自動化** (`04_test_automation/`)
8. **テスト管理** (`05_test_management/`)
9. **最新トレンド** (`06_latest_trends/`)

## 📝 作業ログ管理システム

### 📈 管理ドキュメント
- [`work_log_management_guide.md`](work_log_management_guide.md) ✅ - 作業ログ管理ガイド
- [`daily_log_template.md`](daily_log_template.md) ✅ - 日次学習記録テンプレート
- [`weekly_reflection_template.md`](weekly_reflection_template.md) ✅ - 週次振り返りテンプレート
- [`skill_assessment_template.md`](skill_assessment_template.md) ✅ - スキル評価テンプレート

### 📊 記録すべき6つのカテゴリ
1. **📚 学習内容・進捗** - 時間・内容・方法・進捗率
2. **🎯 理解度・習得度** - 5段階評価で自己評価
3. **🔍 発見・気づき・疑問** - 学びの質を向上
4. **🏋️ 演習・実践記録** - 正答率・間違い分析・改善点
5. **🎯 目標・計画管理** - 計画vs実績・調整事項
6. **🔄 振り返り・改善** - 学習方法の最適化

### 📊 日常的な記録サイクル
- **日次** (5-10分): 基本的な学習記録
- **週次** (15-20分): 包括的な振り返り
- **月次** (30-40分): スキル評価と目標設定

### 💼 実務適用記録
学習した技法を実際の業務で活用した際の記録も重要です：
- 適用場面と効果
- 課題と改善提案
- 他メンバーへの水平展開

## 📊 現在の学習状況

- ✅ 同値分割・境界値分析： 完了
- 🎯 次回：デシジョンテーブル
- 📂 進捗詳細：`progress/learning_plan.md` を参照

## 🗂️ ディレクトリ整理状況

✅ **整理完了** - 重複ディレクトリを統合し、統一された構成に整理済み

❗ **手動削除が必要**：以下のディレクトリをエクスプローラーで削除してください
- `01_基礎/`, `01_test_techniques/`, `02_exercises/`, `03_progress/`, `04_materials/`, `04_自動化/`

## 📚 各ディレクトリ学習ガイド

### 📖 理論学習アウトライン
- [`01_test_fundamentals/learning_outline.md`](01_test_fundamentals/learning_outline.md) - テスト基礎学習ガイド
- [`02_test_techniques/learning_outline.md`](02_test_techniques/learning_outline.md) - テスト技法学習ガイド
- [`03_specialized_testing/learning_outline.md`](03_specialized_testing/learning_outline.md) - 専門領域テスト学習ガイド
- [`04_test_automation/learning_outline.md`](04_test_automation/learning_outline.md) - テスト自動化学習ガイド
- [`05_test_management/learning_outline.md`](05_test_management/learning_outline.md) - テスト管理学習ガイド
- [`06_latest_trends/learning_outline.md`](06_latest_trends/learning_outline.md) - 最新トレンド学習ガイド

### 🛠️ サポートディレクトリガイド
- [`exercises/README.md`](exercises/README.md) - 演習問題活用ガイド
- [`progress/README.md`](progress/README.md) - 学習進捗管理ガイド
- [`materials/README.md`](materials/README.md) - 教材・資料管理ガイド

## 🚀 次回の学習開始方法

新しいチャットで「デシジョンテーブルの演習をお願いします」と伝えてください。

## 📚 学習リソース

- カリキュラム詳細：`materials/curriculum.md`
- 演習問題集：`exercises/`
- 学習記録：`progress/`
- 整理レポート：`cleanup_completion_report.md`

## 💡 学習のコツ

1. **理論学習（30%）** → **実践演習（50%）** → **振り返り（20%）**
2. 各技法は実際の業務シナリオで練習
3. 学習内容は必ず記録して蓄積
4. 疑問点は次回セッションで質問
