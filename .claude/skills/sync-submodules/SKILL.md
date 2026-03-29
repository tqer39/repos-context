---
name: sync-submodules
description: tqer39 の全リポジトリを git submodule として同期する。サブモジュールの追加・更新を行いたいときに実行。
---

# Sync Submodules

tqer39 GitHub アカウントの全リポジトリを git submodule として同期するスキル。

## 重要

**サブモジュールの同期を行う場合は、必ずこのスキルに従って実行すること。**

## 手順

### ステップ 1: リポジトリ一覧の取得

`gh` CLI で tqer39 の全リポジトリを取得する。

```bash
gh repo list tqer39 --limit 1000 --json name,isArchived,isFork,defaultBranchRef
```

### ステップ 2: フィルタリング

取得したリポジトリから以下を除外する:

- `repos-context`（自己参照の防止）
- `isArchived: true` のリポジトリ（アーカイブ済み）
- `isFork: true` のリポジトリ（フォーク）

除外したリポジトリ一覧をユーザーに報告する。

### ステップ 3: 既存サブモジュールの確認

`.gitmodules` ファイルを読み取り、既存のサブモジュール名を一覧化する。

### ステップ 4: 不足サブモジュールの追加

ステップ 2 の結果とステップ 3 の既存一覧を比較し、不足しているリポジトリを追加する。

```bash
git submodule add https://github.com/tqer39/{repo_name} {repo_name}
```

### ステップ 5: 全サブモジュールを最新化

既存・新規を問わず、全サブモジュールでデフォルトブランチの最新を取得する。

各サブモジュールについて、ステップ 1 で取得した `defaultBranchRef.name` を使用して以下を実行:

```bash
git -C {repo_name} fetch origin
git -C {repo_name} checkout {default_branch}
git -C {repo_name} pull origin {default_branch}
```

### ステップ 6: 不要サブモジュールの検出

`.gitmodules` に存在するがステップ 2 の結果に含まれないサブモジュール（削除済み・アーカイブ化されたリポジトリ等）があれば、ユーザーに報告する。**自動削除は行わない。**

### ステップ 7: 変更の確認

```bash
git status
git diff
```

変更内容をユーザーに報告する。**コミットはユーザーの指示を待つ。**

## 注意事項

- `gh` CLI が認証済みであること
- ネットワーク接続が必要
- リポジトリ数が多い場合、追加処理に時間がかかる
- デフォルトブランチはリポジトリごとに異なる（main, master 等）ため、必ず `gh` の情報を使用する
