# Issue 細分化プロンプト テンプレート

このファイルは、親Issueの情報が不足している場合でも、必要な補足情報を明示して Sub-issue を適切に分解するためのテンプレートです。  
生成される各 Sub-issue は **Copilot が実装できる粒度** を目指します。

---

## 📝 使い方

1. [Issue 細分化ジェネレーター](issue-breakdown-generator.html) を開く
2. 下の「設定」を更新（ジェネレーターが自動生成してもよい）
3. 「Copilot への指示（実行用）」をコピーして Copilot に送信
4. 生成されたコマンドを確認して実行

---

## ⚙️ 設定（ここを更新）

```yaml
親Issue番号: "#6"

細分化観点:
- [ ] 機能別分解
- [ ] フェーズ別分解
- [ ] 優先度別分解
- [ ] 担当者別分解
- [ ] カスタム分解
カスタム観点: ""

# 親Issueに十分な情報がない場合に記入
補足情報:
  目的: ""
  背景・課題: ""
  対象範囲（やること）: ""
  対象外（やらないこと）: ""
  完了条件: ""
  制約・注意点: ""

# Sub-issue を実装可能な粒度で作成するために記入（必須）
実装情報:
  技術スタック: ""
  参照ファイル・関連コード: |
    （例: src/components/Form.tsx）
    （例: api/routes/user.ts）
  実装アプローチ: ""
  テスト方針: ""
```

---

## 🎯 Copilot への指示（実行用）

以下をコピーして Copilot に送信してください。

```text
〇〇.mdを読み込んで実行して
```

---

## 📋 Sub-issue 本文の作成方針（Copilot 用）

各 Sub-issue を GitHub CLI で作成する際、`--body` には以下をすべて含めてください。  
**Copilot が実装できる粒度**を意識し、各項目を具体的に記述してください。

**本文フォーマット（各 Sub-issue 共通）:**
```
親 Issue: #[番号]

## 目的
（このタスクで達成すること）

## 実装アプローチ
技術スタック: [技術スタックを記載]
（具体的な実装手順・技術選定の理由）

## 参照ファイル・コード
- （実装に関連するファイルパスを記載）

## テスト方針
（何をどうテストするかを記載）

## 完了条件
（このタスクがDoneになる具体的な条件）
```

**GitHub CLI コマンド形式（PowerShell）:**
```powershell
$gh = "C:\Program Files\GitHub CLI\gh.exe"
$parentId = (& $gh issue view 6 --json id --jq .id)
$childUrl = (& $gh issue create --title "Sub-task: ..." --body "親 Issue: #6...")
$childNumber = ($childUrl -split '/')[-1]
$childId = (& $gh issue view $childNumber --json id --jq .id)
& $gh api graphql -f query='mutation($issueId:ID!,$subIssueId:ID!){ addSubIssue(input:{issueId:$issueId,subIssueId:$subIssueId}) { issue { number } subIssue { number } } }' -f issueId=$parentId -f subIssueId=$childId
```

※ `issue create` だけでは親子関係になりません。必ず `addSubIssue` で親Issueに紐付けてください。

不足情報がある場合は推測せず、「要確認」として明示してください。

---

## 🔄 差分管理モード（追加・更新・Close）

細分化の見直し時は、次の流れで運用します。

1. 現在の Sub-issue 一覧を取得

```powershell
& "C:\Program Files\GitHub CLI\gh.exe" issue view 6 --json number,title,body
& "C:\Program Files\GitHub CLI\gh.exe" issue list --search "parent:#6" --limit 100
```

2. 新しい期待リストを作成（設定を更新）
3. Copilot に差分分析を依頼

```text
親Issue: #6

【現在の Sub-issue】
- #7 Sub-task: 機能A
- #8 Sub-task: 機能C

【新しい期待リスト】
- Sub-task: 機能A（継続）
- Sub-task: 機能B（新規）

【補足情報】
- 目的: ...
- 完了条件: ...

【依頼】
差分を分析して、以下を出力してください。
1. 新規作成コマンド
2. 更新コマンド（必要時）
3. Close対象のコマンド（理由コメント付き）
```

4. Close は必ず理由コメントを先に投稿

```powershell
& "C:\Program Files\GitHub CLI\gh.exe" issue comment 8 --body "細分化見直しにより本Issueは統合対象となったためCloseします。理由: Issue #7 に統合"
& "C:\Program Files\GitHub CLI\gh.exe" issue close 8
```

---

## ✅ Close理由コメントの雛形

```text
細分化の見直しにより、本Issueはクローズします。

【理由】
- （例）Issue #7 に統合
- （例）対象範囲外となったため
- （例）優先度見直しで次フェーズへ移動

【参照】
- 親 Issue: #6
- 代替/統合先: #7
```

---

作成日: 2026-05-13  
用途: 親Issueの情報不足を補完しながら Sub-issue を生成・更新・整理する
