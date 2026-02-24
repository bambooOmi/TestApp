# Repository Conventions
## 一問一答フォーム作成・分析ツール

---

# 1. 本書の目的

本ドキュメントは、本プロジェクトの
GitHubリポジトリ運用ルールを定義する。

目的は以下の通り：

- ソースコードと設計の整合性維持
- 将来的な保守性確保
- 履歴追跡可能な開発体制の確立

---

# 2. リポジトリ構成

root/
│
├── src/                        # GASソースコード
│   ├── domain/                 # 業務ロジック（採点・集計）
│   ├── application/            # ユースケース処理
│   ├── infrastructure/         # Spreadsheet / Form連携
│   ├── presentation/           # エントリポイント・トリガー
│   └── appsscript.json
│
├── docs/                       # 設計・仕様ドキュメント
│   │
│   ├── phase0/                 # Phase0（企画）
│   │   ├── phase0-charter.md
│   │   ├── mvp-scope.md
│   │   ├── highlevel-requirements.md
│   │   ├── feasibility-memo.md
│   │   └── repo-conventions.md
│   │
│   └── phase1/                 # Phase1（要求定義）
│       ├── use-cases.md
│       ├── acceptance-criteria.md
│       ├── data-requirements.md
│       ├── permission-model.md
│       ├── requirements-spec.md
│       └── test-scenarios.md
│
├── diagrams/                   # 図ファイル
│   ├── architecture.puml
│   ├── er-diagram.puml
│   └── *.drawio
│
├── samples/                    # サンプルデータ
│
└── README.md

---

# 3. ブランチ戦略

## 基本方針

- `main`：常に動作可能な状態を維持
- `feature/*`：機能開発用ブランチ

## 開発フロー

1. Issue作成
2. `feature/issue-番号-概要` ブランチ作成
3. 実装
4. Pull Request作成
5. レビュー（自己レビュー含む）
6. mainへマージ

---

### 4. コミット規約

### メッセージ形式

    <type>: <summary>

    <body>

### type例

- feat: 新機能
- fix: バグ修正
- refactor: 構造変更（仕様変更なし）
- docs: ドキュメント更新
- chore: その他雑務

### 例

    feat: add quiz generation service
    fix: correct scoring logic for multiple choice
    docs: update highlevel requirements

---

## 5. GAS開発方針

### 5.1 clasp利用

- claspでローカル開発
- appsscript.json は src 直下に配置
- デプロイは手動確認後に実施

### 5.2 コード構造原則

- 業務ロジック（採点・集計）は domain 層へ
- GAS API呼び出しは infrastructure 層へ
- トリガー関数は presentation 層へ

---

## 6. 命名規則

### 6.1 ファイル名

- camelCase.js
- 1ファイル1責務

### 6.2 ID命名

- questionId
- quizId
- attemptId

UUID形式を推奨する。

### 6.3 シート名

- Questions
- Quizzes
- QuizQuestions
- Attempts
- AttemptAnswers
- Aggregates

※ 名称は変更しないこと。

---

## 7. Issue管理

### Issue分類

- Feature
- Bug
- Refactor
- Documentation
- Research

### Issueテンプレ（簡易）

- 背景
- 目的
- 完了条件
- 関連資料

---

## 8. 設計変更ルール

以下を変更する場合は、必ず docs を更新すること：

- データ構造
- ID設計
- 採点ロジック
- 権限設計

設計変更なしに実装変更しないこと。

---

## 9. バージョニング

- v0.x.x → MVP開発中
- v1.0.0 → 初期正式版

---

## 10. 原則

- 設計が先、実装が後
- データ設計は最優先
- 一時的実装を放置しない
- ドキュメントとコードを一致させる

---

Author: Takeomi Tamura
Date: 2026/2/23
Version: 0.1