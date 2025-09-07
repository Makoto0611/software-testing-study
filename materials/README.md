# 教材・資料 ディレクトリガイド

## 📚 目的
- 学習に必要な資料を体系的に管理する
- 参考文献・ツール情報を整理する
- 知識ベースとして活用する

## 📁 教材ファイル構成

### 既存ファイル
- `curriculum.md` ✅ - 詳細カリキュラム（完全版）
- `archived_artifact.md` ✅ - 過去の学習アーティファクト

### 推奨追加ファイル
- `reference_books.md` ⏳ - 参考書籍リスト
- `online_resources.md` ⏳ - オンラインリソース集
- `tools_and_software.md` ⏳ - テストツール情報
- `glossary.md` ⏳ - 用語集
- `best_practices.md` ⏳ - ベストプラクティス集
- `case_studies.md` ⏳ - 実例・ケーススタディ

## 📖 資料カテゴリ

### 1. 基礎理論資料
- **ISTQB関連**
  - Foundation Level シラバス
  - Advanced Level 各専門分野
  - 用語集（英語・日本語）

- **テスト技法書籍**
  - ソフトウェアテスト技法練習帳
  - テスト駆動開発入門
  - 実践アジャイルテスト

### 2. 実践・ツール資料
- **自動化ツール**
  - Selenium Documentation
  - Cypress Guides
  - JMeter User Manual

- **管理ツール**
  - TestRail ガイド
  - Jira テスト管理
  - Quality Center マニュアル

### 3. 専門分野資料
- **パフォーマンステスト**
  - 性能テスト実践ガイド
  - JMeter実践レシピ
  - Gatling公式ドキュメント

- **セキュリティテスト**
  - OWASP Testing Guide
  - セキュリティテスト実践
  - ペネトレーションテスト入門

### 4. 最新技術資料
- **AI・機械学習**
  - MLテスト論文集
  - AI品質保証ガイド
  - モデル検証手法

- **クラウド・DevOps**
  - クラウドテスト戦略
  - CI/CDベストプラクティス
  - Infrastructure as Code

## 🔧 ツール・ソフトウェア情報

### テスト設計ツール
| ツール名 | 用途 | ライセンス | URL |
|----------|------|------------|-----|
| TestLink | テストケース管理 | GPL | https://testlink.org/ |
| PictMaster | 組み合わせテスト | 商用 | - |
| CATS | 組み合わせテスト | フリー | - |

### 自動化ツール
| ツール名 | 対象 | 言語 | URL |
|----------|------|------|-----|
| Selenium | Webアプリ | 多言語 | https://selenium.dev/ |
| Cypress | Webアプリ | JavaScript | https://cypress.io/ |
| Appium | モバイル | 多言語 | https://appium.io/ |

### パフォーマンステストツール
| ツール名 | 特徴 | ライセンス | URL |
|----------|------|------------|-----|
| JMeter | 汎用性高い | Apache | https://jmeter.apache.org/ |
| Gatling | 高性能 | Apache | https://gatling.io/ |
| k6 | 開発者向け | AGPL | https://k6.io/ |

## 📚 学習リソース階層

### レベル1: 入門
- ISTQB Foundation Level
- テスト技法基礎書籍
- オンライン無料コース

### レベル2: 実践
- 専門技法書籍
- ツール公式ドキュメント
- 実践的なチュートリアル

### レベル3: 専門
- 専門領域論文
- カンファレンス資料
- 最新研究成果

### レベル4: 最先端
- 研究論文
- 技術ブログ
- 業界レポート

## 🌐 オンラインリソース

### 公式サイト・組織
- **ISTQB**: https://istqb.org/
- **JSTQB**: https://jstqb.jp/
- **OWASP**: https://owasp.org/
- **Test Automation University**: https://testautomationu.applitools.com/

### コミュニティ・フォーラム
- Stack Overflow (testing tags)
- Reddit r/QualityAssurance
- Ministry of Testing Community
- Japanese Testing Community

### ブログ・メディア
- Google Testing Blog
- Microsoft Testing Blog
- Ministry of Testing Blog
- Software Testing Help

## 📝 資料管理方法

### ファイル命名規則
```
[カテゴリ]_[技法名]_[詳細].[拡張子]
例: theory_blackbox_decision-table.md
    tool_selenium_quickstart-guide.pdf
    case_api-testing_ecommerce-example.md
```

### バージョン管理
- 資料更新時は更新日を記録
- 古い情報は archived/ フォルダに移動
- 最新トレンドは定期的に見直し

### タグ付け
各資料に以下のタグを付与：
- **レベル**: 入門/基礎/応用/専門
- **技法**: 対象となるテスト技法
- **ツール**: 関連ツール名
- **更新日**: 最終更新日

## 🔗 関連ディレクトリ
- `01_test_fundamentals/` ～ `06_latest_trends/` - 各分野の学習内容
- `exercises/` - 実践演習
- `progress/` - 学習記録

## 💡 効果的な資料活用のコツ
1. **体系的な整理**: カテゴリ・レベル別に分類
2. **定期的な見直し**: 古い情報の更新・削除
3. **実践との連携**: 理論学習と演習を関連付け
4. **個人的なメモ**: 理解のポイントを追記
