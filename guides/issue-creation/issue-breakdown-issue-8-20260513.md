# Issue 細分化プロンプト テンプレート

このファイルは、親Issueの情報が不足している場合でも、必要な補足情報を明示して Sub-issue を適切に分解するためのテンプレートです。

---

## 📝 使い方

1. 下の「設定」を更新
2. 「Copilot への指示（実行用）」をコピー
3. 設定値を反映して Copilot チャットに貼り付け
4. 生成されたコマンドを確認して実行

---

## ⚙️ 設定（ここを更新）

```yaml
親Issue番号: "#8"

細分化観点:
- [x] 機能別分解
- [x] フェーズ別分解
- [ ] 優先度別分解
- [ ] 担当者別分解
- [ ] カスタム分解
カスタム観点: ""

# 親Issueに十分な情報がない場合に記入
補足情報:
  目的: "保護者ポータルの定期テストが入力できるようにしたい"
  背景・課題: "紙で提出してシステムに入力しているので不便"
  対象範囲（やること）: "画面実装・API実装"
  対象外（やらないこと）: ""
  完了条件: ""
  制約・注意点: ""
```

---

## 🎯 Copilot への指示（実行用）

以下をコピーして Copilot に送信してください。

```text
issue-breakdown-issue-8-20260513.mdを読み込んで実行して
```

---

## 🔄 差分管理モード（追加・更新・Close）

細分化の見直し時は、次の流れで運用します。

1. 現在の Sub-issue 一覧を取得

```powershell
& "C:Program FilesGitHub CLIgh.exe" issue view 8 --json number,title,body
& "C:Program FilesGitHub CLIgh.exe" issue list --search "parent:#8" --limit 100
```

2. 新しい期待リストを作成（設定を更新）
3. Copilot に差分分析を依頼

```text
親Issue: #8

【現在の Sub-issue】
- #7 Sub-task: 機能A
- #8 Sub-task: 機能C

【新しい期待リスト】
- Sub-task: 機能A（継続）
- Sub-task: 機能B（新規）

【補足情報】
- 目的: 保護者ポータルの定期テストが入力できるようにしたい
- 完了条件: ...

【依頼】
差分を分析して、以下を出力してください。
1. 新規作成コマンド
2. 更新コマンド（必要時）
3. Close対象のコマンド（理由コメント付き）
```

4. Close は必ず理由コメントを先に投稿

```powershell
& "C:Program FilesGitHub CLIgh.exe" issue comment 8 --body "細分化見直しにより本Issueは統合対象となったためCloseします。理由: Issue #7 に統合"
& "C:Program FilesGitHub CLIgh.exe" issue close 8
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
- 親 Issue: #8
- 代替/統合先: #7
```

---

作成日: 2026-05-13
用途: 親Issueの情報不足を補完しながら Sub-issue を生成・更新・整理する