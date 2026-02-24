# Data Requirements
## 一問一答フォーム作成・分析ツール（Phase1）

---

# 1. 本書の目的

本書は、本システムにおけるデータ要件を定義する。

目的は以下の通り：

- 保持すべきデータの明確化
- 識別子（ID）設計の確定
- データ整合性ルールの定義
- 将来のIRT拡張を妨げない構造の確保

---

# 2. データ設計原則

1. IDはすべて一意（UUID推奨）
2. Questionは原則不変（内容変更は複製）
3. Attemptはすべて保存（再受験含む）
4. AttemptAnswersは正規化して保存
5. PIIは最小限

---

# 3. 識別子（ID）要件

| ID名 | 対象 | 要件 |
|------|------|------|
| questionId | Question | 一意・不変 |
| quizId | Quiz | 一意 |
| attemptId | Attempt | 一意 |
| userId | 生徒 | Googleアカウントを識別子とする |

## 3.1 ID生成方式

- UUID v4推奨
- GASのUtilities.getUuid()利用可

---

# 4. データエンティティ定義

---

## 4.1 Questions

### 目的
問題の定義を保持する。

### 属性

- questionId (PK)
- questionText
- choices（JSONまたは分割列）
- correctAnswer
- explanation（任意）
- tags（任意）
- difficulty（任意）
- createdAt

### 制約

- questionIdは変更不可
- 内容変更は複製し新questionIdを発行

---

## 4.2 Quizzes

### 目的
問題セット（小テスト）を保持する。

### 属性

- quizId (PK)
- title
- createdBy
- createdAt
- formId
- responseSpreadsheetId
- deadline（任意）

---

## 4.3 QuizQuestions

### 目的
QuizとQuestionの関連を保持する。

### 属性

- quizId (FK)
- questionId (FK)
- displayOrder

### 制約

- 同一quizId内でquestionIdは重複不可

---

## 4.4 Attempts

### 目的
生徒によるクイズ回答単位を保持する。

### 属性

- attemptId (PK)
- quizId (FK)
- userId（Googleアカウント）
- submittedAt
- totalScore
- isFirstSeenForUserQuestion（bool）

### 制約

- 同一userIdによる複数attemptを許可
- すべて保存する

---

## 4.5 AttemptAnswers

### 目的
Attempt内の設問単位結果を保持する。

### 属性

- attemptId (FK)
- questionId (FK)
- response
- isCorrect
- score（1固定）
- answeredAt

### 制約

- 必ず対応するAttemptが存在する
- 必ず対応するQuestionが存在する

---

# 5. PII（個人情報）要件

## 5.1 識別方式

- Googleアカウントを基本識別子とする

## 5.2 氏名・学生ID

- Googleアカウントと学生ID・氏名の対応付けは可能
- ただし必須ではない

## 5.3 スプレッドシート共有

- 生徒は編集不可
- 教師のみ編集可能

---

# 6. データ整合性要件

## 6.1 参照整合性

- QuizQuestionsは存在するQuiz・Questionを参照
- Attemptsは存在するQuizを参照
- AttemptAnswersは存在するAttempt・Questionを参照

## 6.2 削除ポリシー

- Question削除は禁止（過去データ保護）
- Quiz削除は論理削除推奨
- Attempt削除は管理者操作のみ許可
- 物理削除は禁止
- AttemptおよびAttemptAnswersは論理削除のみ
- isDeleted / deletedAt / deletedBy / deleteReasonを保持
- Attempt論理削除時はAttemptAnswersも連動論理削除

---

# 7. 集計関連データ

## 7.1 正答率算出

正答率 = 正答数 / 回答数  
（未回答は分母に含めない）

## 7.2 クラス平均

Attempt単位で算出する。

---

# 8. 将来拡張への備え

MVP段階で以下を保証する：

- AttemptAnswersが設問単位で保存されている
- 初回Attempt識別可能（IRT導入準備）
- Questionは不変設計

---

# 9. 想定データ規模

- 30名 × 10問 × 月4回 × 年12ヶ月
≈ 14,400 AttemptAnswerレコード

スプレッドシートで対応可能範囲。

---

Author: Takeomi Tamura
Date: 2026/2/23
Version: 0.1