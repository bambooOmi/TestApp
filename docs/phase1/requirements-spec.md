# Requirements Specification
## 一問一答フォーム作成・分析ツール（Phase1 確定版）

---

# 1. 本書の目的

本書は、本プロジェクトMVPにおける
機能要件・非機能要件を統合的に定義するものである。

本書は以下を統合する：

- use-cases.md
- acceptance-criteria.md
- data-requirements.md
- permission-model.md
- requirements-decisions.md

本書に記載された内容を満たした場合、
MVPは要件充足と判断される。

---

# 2. システム概要

本システムは、Google Workspace（Googleフォーム / Google Apps Script / スプレッドシート）を基盤とし、

- 問題作成
- クイズ作成
- フォーム自動生成
- 回答収集
- 自動採点
- 集計・分析

を行う学習支援ツールである。

---

# 3. アクター定義

| ロール | 説明 |
|--------|------|
| Teacher | 問題作成、クイズ作成、配布、分析を行う |
| Student | Googleアカウントで回答する |

---

# 4. 機能要件（Functional Requirements）

---

## FR-01 問題登録

### 要件

- 教師は問題を登録できる
- 必須項目：問題文、選択肢、正解
- questionIdは一意である
- 登録後は編集可能だが内容的変更は禁止
- 内容変更が必要な場合は複製して新questionIdを発行する

---

## FR-02 クイズ作成

### 要件

- 教師は登録済み問題からクイズを作成できる
- quizIdは一意である
- QuizQuestionsで紐付け管理する
- 問題0件では作成不可

---

## FR-03 フォーム生成

### 要件

- QuizからGoogleフォームを自動生成できる
- Googleアカウント限定回答（ただしドメイン限定はしない）
- メールアドレスを収集する
- 回答先スプレッドシートを紐付ける
- 複数回回答を許可する
- 生成したformIdを保存する
- quizIdとformIdの対応関係を保持する
- responseSpreadsheetIdも保存する

---

## FR-04 回答管理

### 要件

- 生徒はGoogleアカウントで回答できる
- 複数回回答を許可する
- すべてのAttemptを保存する
- attemptIdを一意に付与する

---

## FR-05 自動採点

### 要件

- 送信時に正誤判定を行う
- 1問1点固定
- 合計得点を保存する
- AttemptAnswersを正規化保存する
- 初回Attemptを識別可能とする（IRT準備）
- 決定：初回判定は「その userId がその questionId に初めて回答した AttemptAnswer」
- 実装上の扱い（設計方針）
  - Attempt単位の isFirstAttempt は意味が変わるので、IRT用途は AttemptAnswers側で管理するのが整合的
    - 例：AttemptAnswers に isFirstSeenForUserQuestion を持つ
  - 初回判定は「保存時に過去AttemptAnswersを検索して決める」か「後でバッチで付与」かを Phase2 で決める

---

## FR-06 集計

### 要件

- 設問別正答率を算出する
- 正答率 = 正答数 / 回答数
- 未回答は分母に含めない
- クラス平均はAttempt単位で算出する
- 個人別得点一覧を取得可能

---

## FR-07 エラーハンドリング

### 要件

- トリガー失敗時にログ記録する
- 教師へ通知する
- データ不整合を検知可能とする
- 必要に応じてデータ削除可能とする
  - 決定：物理削除は禁止、論理削除のみ
  - 論理削除の典型列
    - isDeleted（bool）
    - deletedAt（timestamp）
    - deletedBy（teacher email）
    - deleteReason（任意）
  - 連動：Attemptを論理削除したら、AttemptAnswersも同一attemptIdで一括論理削除

---

# 5. 非機能要件（Non-Functional Requirements）

---

## NFR-01 セキュリティ

- 生徒はスプレッドシートにアクセスできない
- 教師のみ編集可能
- Googleアカウント制限を有効にする
- PIIは最小限とする

---

## NFR-02 性能

- 30名 × 10問規模で正常動作
- 採点処理は10秒以内
- 集計反映は30秒以内

---

## NFR-03 可用性

- Google Workspace標準可用性に準拠
- 手動バックアップ可能

---

## NFR-04 保守性

- IDはUUID形式
- 層分離（domain / application / infrastructure / presentation）
- 設計変更時はdocs更新必須

---

## NFR-05 拡張性

- AttemptAnswers正規化保存
- Question不変設計
- IRT導入可能な構造

---

# 6. データ要件（概要）

| エンティティ | 説明 |
|-------------|------|
| Questions | 問題定義 |
| Quizzes | クイズ定義 |
| QuizQuestions | 関連管理 |
| Attempts | 回答単位 |
| AttemptAnswers | 設問単位結果 |

詳細は data-requirements.md を参照。

---

# 7. 制約条件

- Google Apps Scriptを使用
- データストアはGoogle Spreadsheet
- フォームはGoogle Form
- GitHubでソース管理

---

# 8. 未対象（Out of Scope）

- IRT本格実装
- 高度ダッシュボードUI
- クラス管理機能
- 学生成績推移分析

---

# 9. 受入判定基準

acceptance-criteria.md に準拠する。

---

Author: Takeomi Tamura
Date: 2026/2/23
Version: 0.1