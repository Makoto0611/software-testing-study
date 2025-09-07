# ソフトウェアテスト技術学習プロジェクト

## 📁 ディレクトリ構成

```
testing/
├── 01_test_fundamentals/               # テスト基礎
│   ├── 01_principles_and_concepts/     # テストの原則と概念
│   └── 02_test_process/                # テストプロセス
├── 02_test_techniques/                 # テスト技法
│   ├── 01_static_testing/              # 静的テスト
│   │   ├── 01_review/                  # レビュー技法
│   │   └── 02_static_analysis/         # 静的解析
│   └── 02_dynamic_testing/             # 動的テスト
│       ├── 01_blackbox/                # ブラックボックステスト
│       │   ├── 01_equivalence_partitioning/  # 同値分割
│       │   ├── 02_boundary_value_analysis/   # 境界値分析
│       │   └── 03_decision_table/            # デシジョンテーブル
│       ├── 02_whitebox/                # ホワイトボックステスト
│       │   ├── 01_statement_coverage/  # 命令網羅
│       │   ├── 02_branch_coverage/     # 分岐網羅
│       │   └── 03_condition_coverage/  # 条件網羅
│       └── 03_experience_based/        # 経験ベーステスト
│           ├── 01_exploratory_testing/ # 探索的テスト
│           └── 02_error_guessing/      # エラー推測
├── 03_specialized_testing/             # 専門領域テスト
│   ├── 01_performance_testing/         # パフォーマンステスト
│   ├── 02_security_testing/            # セキュリティテスト
│   └── 03_mobile_api_db_cloud/         # モバイル/API/DB/クラウド
├── 04_test_automation/                 # テスト自動化
│   ├── 01_roi_analysis/                # ROI分析
│   ├── 02_frameworks/                  # フレームワーク
│   └── 03_cicd_integration/            # CI/CD統合
├── 05_test_management/                 # テスト管理
│   ├── 01_planning/                    # 計画
│   ├── 02_execution_management/        # 実行管理
│   └── 03_metrics/                     # メトリクス
├── 06_latest_trends/                   # 最新トレンド
│   ├── 01_ai_testing/                  # AIテスト
│   ├── 02_big_data/                    # ビッグデータ
│   └── 03_iot/                         # IoT
├── exercises/                          # 演習問題
├── progress/                           # 学習進捗管理
└── materials/                          # 教材・資料
```

## 🎯 推奨学習順序

### Phase 1: 基礎固め
1. **テスト基礎** (`01_test_fundamentals/`)
   - 01_principles_and_concepts/ (テストの原則と概念)
   - 02_test_process/ (テストプロセス)

### Phase 2: テスト技法習得
2. **ブラックボックステスト** (`02_test_techniques/02_dynamic_testing/01_blackbox/`)
   - 01_equivalence_partitioning/ (同値分割) ✅
   - 02_boundary_value_analysis/ (境界値分析) ✅
   - **03_decision_table/ (デシジョンテーブル)** ← 次回学習予定
3. **静的テスト** (`02_test_techniques/01_static_testing/`)
   - 01_review/ (レビュー技法)
   - 02_static_analysis/ (静的解析)
4. **ホワイトボックステスト** (`02_test_techniques/02_dynamic_testing/02_whitebox/`)
   - 01_statement_coverage/ (命令網羅)
   - 02_branch_coverage/ (分岐網羅)
   - 03_condition_coverage/ (条件網羅)
5. **経験ベーステスト** (`02_test_techniques/02_dynamic_testing/03_experience_based/`)
   - 01_exploratory_testing/ (探索的テスト)
   - 02_error_guessing/ (エラー推測)

### Phase 3: 専門分野
6. **専門領域テスト** (`03_specialized_testing/`)
   - 01_performance_testing/ (パフォーマンステスト)
   - 02_security_testing/ (セキュリティテスト)
   - 03_mobile_api_db_cloud/ (モバイル/API/DB/クラウド)
7. **テスト自動化** (`04_test_automation/`)
   - 01_roi_analysis/ (ROI分析)
   - 02_frameworks/ (フレームワーク)
   - 03_cicd_integration/ (CI/CD統合)
8. **テスト管理** (`05_test_management/`)
   - 01_planning/ (計画)
   - 02_execution_management/ (実行管理)
   - 03_metrics/ (メトリクス)
9. **最新トレンド** (`06_latest_trends/`)
   - 01_ai_testing/ (AIテスト)
   - 02_big_data/ (ビッグデータ)
   - 03_iot/ (IoT)

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

✅ **整理完了** - 項番付きの統一されたディレクトリ構成に更新

❗ **手動でのフォルダ名変更が必要**：
以下のディレクトリ名を項番付きに変更してください：

### 第1階層
- `test_fundamentals/` → `01_test_fundamentals/`
- `test_techniques/` → `02_test_techniques/`
- `specialized_testing/` → `03_specialized_testing/`
- `test_automation/` → `04_test_automation/`
- `test_management/` → `05_test_management/`
- `latest_trends/` → `06_latest_trends/`

### 第2階層（01_test_fundamentals/）
- `principles_and_concepts/` → `01_principles_and_concepts/`
- `test_process/` → `02_test_process/`

### 第2階層（02_test_techniques/）
- `static_testing/` → `01_static_testing/`
- `dynamic_testing/` → `02_dynamic_testing/`

### 第3階層（01_static_testing/）
- `review/` → `01_review/`
- `static_analysis/` → `02_static_analysis/`

### 第3階層（02_dynamic_testing/）
- `blackbox/` → `01_blackbox/`
- `whitebox/` → `02_whitebox/`
- `experience_based/` → `03_experience_based/`

### 第4階層（01_blackbox/）
- `equivalence_partitioning/` → `01_equivalence_partitioning/`
- `boundary_value_analysis/` → `02_boundary_value_analysis/`
- `decision_table/` → `03_decision_table/`

### 第4階層（02_whitebox/）
- `statement_coverage/` → `01_statement_coverage/`
- `branch_coverage/` → `02_branch_coverage/`
- `condition_coverage/` → `03_condition_coverage/`

### 第4階層（03_experience_based/）
- `exploratory_testing/` → `01_exploratory_testing/`
- `error_guessing/` → `02_error_guessing/`

### 第2階層（03_specialized_testing/）
- `performance_testing/` → `01_performance_testing/`
- `security_testing/` → `02_security_testing/`
- `mobile_api_db_cloud/` → `03_mobile_api_db_cloud/`

### 第2階層（04_test_automation/）
- `roi_analysis/` → `01_roi_analysis/`
- `frameworks/` → `02_frameworks/`
- `cicd_integration/` → `03_cicd_integration/`

### 第2階層（05_test_management/）
- `planning/` → `01_planning/`
- `execution_management/` → `02_execution_management/`
- `metrics/` → `03_metrics/`

### 第2階層（06_latest_trends/）
- `ai_testing/` → `01_ai_testing/`
- `big_data/` → `02_big_data/`
- `iot/` → `03_iot/`

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