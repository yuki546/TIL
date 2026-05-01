# Git 基本コマンド チートシート

Git でよく使う基本コマンドをまとめたチートシート。

## 初期設定

```bash
# ユーザー名とメールアドレスの設定
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 設定の確認
git config --list
```

## リポジトリの作成・取得

```bash
# 新しいリポジトリを初期化
git init

# 既存のリポジトリをクローン
git clone <リポジトリURL>
```

## 変更の記録

```bash
# ファイルの状態を確認
git status

# 変更をステージングエリアに追加
git add <ファイル名>          # 特定のファイルを追加
git add .                    # すべての変更を追加
git add -A                   # すべての変更を追加（削除も含む）

# ステージングエリアから削除
git reset <ファイル名>

# 変更をコミット
git commit -m "コミットメッセージ"

# ステージングとコミットを同時に実行（追跡済みファイルのみ）
git commit -am "コミットメッセージ"
```

## 変更の確認

```bash
# 変更内容の差分を表示
git diff                     # 作業ディレクトリとステージングエリアの差分
git diff --staged            # ステージングエリアと最新コミットの差分

# コミット履歴を表示
git log                      # 詳細なログ
git log --oneline            # 1行形式のログ
git log --graph --oneline    # グラフ形式のログ
```

## ブランチ操作

```bash
# ブランチ一覧を表示
git branch                   # ローカルブランチ
git branch -a                # すべてのブランチ（リモート含む）

# ブランチを作成
git branch <ブランチ名>

# ブランチを切り替え
git switch <ブランチ名>
git checkout <ブランチ名>    # 旧コマンド

# ブランチを作成して切り替え
git switch -c <ブランチ名>
git checkout -b <ブランチ名> # 旧コマンド

# ブランチを削除
git branch -d <ブランチ名>   # マージ済みブランチのみ
git branch -D <ブランチ名>   # 強制削除

# ブランチをマージ
git merge <ブランチ名>
```

## リモートリポジトリ操作

```bash
# リモートリポジトリを表示
git remote -v

# リモートリポジトリを追加
git remote add origin <リポジトリURL>

# リモートから最新情報を取得（マージしない）
git fetch

# リモートから最新情報を取得してマージ
git pull

# リモートにプッシュ
git push                            # 現在のブランチをプッシュ
git push origin <ブランチ名>        # 特定のブランチをプッシュ
git push -u origin <ブランチ名>     # 初回プッシュ時（追跡設定）
```

## 変更の取り消し

```bash
# 作業ディレクトリの変更を取り消し
git restore <ファイル名>
git checkout -- <ファイル名>  # 旧コマンド

# ステージングを取り消し（変更は保持）
git restore --staged <ファイル名>
git reset HEAD <ファイル名>   # 旧コマンド

# 直前のコミットを修正
git commit --amend

# コミットを取り消し
git reset --soft HEAD~1      # コミットのみ取り消し（変更は保持）
git reset --mixed HEAD~1     # ステージングも取り消し（デフォルト）
git reset --hard HEAD~1      # 変更も完全に削除（注意！）
```

## その他の便利なコマンド

```bash
# 一時的に変更を退避
git stash                    # 変更を退避
git stash list               # 退避リストを表示
git stash pop                # 退避した変更を復元
git stash drop               # 退避した変更を削除

# タグの作成
git tag <タグ名>             # 軽量タグ
git tag -a <タグ名> -m "メッセージ"  # 注釈付きタグ
git push origin <タグ名>     # タグをプッシュ
```

## よく使うコマンドフロー

### 基本的な作業フロー

```bash
# 1. 変更を確認
git status

# 2. 変更をステージング
git add .

# 3. コミット
git commit -m "変更内容の説明"

# 4. リモートにプッシュ
git push
```

### ブランチを使った作業フロー

```bash
# 1. 新しいブランチを作成して切り替え
git switch -c feature/new-feature

# 2. 作業してコミット
git add .
git commit -m "新機能を追加"

# 3. リモートにプッシュ
git push -u origin feature/new-feature

# 4. main ブランチに戻る
git switch main

# 5. ブランチをマージ
git merge feature/new-feature
```

## Tips

- **コミットメッセージ**: 何を変更したか明確に書く
- **こまめなコミット**: 小さな単位で頻繁にコミットする
- **プッシュ前の確認**: `git status` と `git diff` で変更を確認
- **ブランチ活用**: main ブランチで直接作業せず、ブランチを切る習慣をつける

## 参考

- [Git 公式ドキュメント](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com)
