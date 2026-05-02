# GitHub Flow

## GitHub Flow とは

GitHub Flow は、シンプルで効果的な開発ワークフロー。Git Flow よりも軽量で、継続的デプロイメント（CD）に適している。

## 基本の流れ

### 1. main から feature ブランチを作成

まず、最新の main ブランチを取得してから、新しい機能ブランチを作成する。

```bash
# main ブランチに移動
git checkout main

# 最新の状態を取得
git pull origin main

# 新しいブランチを作成して移動
git checkout -b feature/add-user-profile
```

**ブランチ名の命名規則の例:**
- `feature/機能名` - 新機能追加
- `fix/修正内容` - バグ修正
- `hotfix/緊急修正` - 緊急の修正
- `docs/ドキュメント名` - ドキュメント更新

### 2. feature ブランチで作業・コミット

ブランチ上で作業を進め、意味のある単位でコミットを作成する。

```bash
# ファイルの変更状況を確認
git status

# 変更をステージングエリアに追加
git add src/components/UserProfile.js

# または、すべての変更を追加
git add .

# コミットを作成（わかりやすいメッセージを付ける）
git commit -m "Add: ユーザープロフィール表示機能を追加"

# リモートにプッシュ
git push origin feature/add-user-profile
```

**コミットメッセージのベストプラクティス:**
- `Add:` - 新規機能追加
- `Update:` - 機能の更新
- `Fix:` - バグ修正
- `Remove:` - 削除
- `Refactor:` - リファクタリング

### 3. プルリクエスト（PR）を作成

GitHub 上でプルリクエストを作成する。

**CLI を使う場合（GitHub CLI）:**

```bash
# GitHub CLI でプルリクエストを作成
gh pr create --title "ユーザープロフィール表示機能を追加" \
  --body "## 変更内容

  - ユーザープロフィールコンポーネントを追加
  - プロフィール画像の表示機能を実装

  ## テスト

  - [ ] ローカルでの動作確認
  - [ ] レスポンシブデザインの確認"
```

**Web UI を使う場合:**
1. GitHub リポジトリページにアクセス
2. "Pull requests" タブをクリック
3. "New pull request" ボタンをクリック
4. base: `main` ← compare: `feature/add-user-profile` を選択
5. タイトルと説明を記入して "Create pull request" をクリック

### 4. レビュー・議論

チームメンバーからフィードバックを受け取り、必要に応じて修正する。

```bash
# レビュー指摘事項を修正
git add src/components/UserProfile.js
git commit -m "Fix: レビュー指摘事項を修正"
git push origin feature/add-user-profile
```

**PR のレビューを確認:**

```bash
# PR の状態を確認
gh pr status

# PR の詳細を表示
gh pr view 123

# PR のチェックアウト（レビュアーが確認する場合）
gh pr checkout 123
```

### 5. main にマージ

レビューが承認されたら、main ブランチにマージする。

**GitHub 上でマージ:**
1. "Merge pull request" ボタンをクリック
2. マージ方法を選択（通常は "Squash and merge" または "Create a merge commit"）
3. "Confirm merge" をクリック

**CLI でマージ:**

```bash
# PR をマージ
gh pr merge 123 --squash

# または通常のマージ
gh pr merge 123 --merge

# または rebase
gh pr merge 123 --rebase
```

**ローカルでマージ（推奨されない）:**

```bash
# main ブランチに移動
git checkout main

# 最新を取得
git pull origin main

# feature ブランチをマージ
git merge feature/add-user-profile

# リモートにプッシュ
git push origin main
```

### 6. feature ブランチを削除

マージ後、不要になったブランチを削除する。

```bash
# リモートブランチを削除（GitHub でマージ時に自動削除されることが多い）
git push origin --delete feature/add-user-profile

# ローカルブランチを削除
git branch -d feature/add-user-profile

# または強制削除（マージされていない場合）
git branch -D feature/add-user-profile
```

**リモートで削除されたブランチをローカルから削除:**

```bash
# リモートで削除されたブランチの参照を削除
git fetch --prune

# または
git remote prune origin
```

## 実践例：完全なワークフロー

実際の開発を想定した完全な流れ：

```bash
# 1. 最新の main を取得
git checkout main
git pull origin main

# 2. 新しい機能ブランチを作成
git checkout -b feature/add-search-function

# 3. コードを書く
# （エディタで作業）

# 4. 変更をコミット
git add src/components/Search.js
git commit -m "Add: 検索機能のUIコンポーネントを追加"

# 5. さらに作業
git add src/api/search.js
git commit -m "Add: 検索APIとの連携処理を追加"

# 6. リモートにプッシュ
git push origin feature/add-search-function

# 7. プルリクエストを作成
gh pr create --title "検索機能を追加" --body "ユーザーが記事を検索できる機能を追加しました"

# 8. レビュー後、修正が必要な場合
git add .
git commit -m "Fix: レビュー指摘事項を修正"
git push origin feature/add-search-function

# 9. 承認されたらマージ（GitHub UI または CLI）
gh pr merge --squash

# 10. ブランチを削除
git checkout main
git pull origin main
git branch -d feature/add-search-function
```

## よくある操作

### 作業途中で main の変更を取り込む

```bash
# 現在の作業をコミット
git add .
git commit -m "Work in progress"

# main の最新を取り込む
git checkout main
git pull origin main
git checkout feature/add-user-profile
git rebase main

# または merge
git merge main

# コンフリクトが発生した場合は解決後
git add .
git rebase --continue
# または
git commit -m "Merge main into feature branch"
```

### 間違ったコミットを修正する

```bash
# 直前のコミットメッセージを修正
git commit --amend -m "修正したメッセージ"

# 直前のコミットにファイルを追加
git add 忘れたファイル.js
git commit --amend --no-edit

# プッシュ済みの場合は強制プッシュ（注意！）
git push origin feature/add-user-profile --force-with-lease
```

### PR の状態を確認

```bash
# 自分の PR 一覧を表示
gh pr list --author @me

# 特定の PR の詳細を表示
gh pr view 123

# PR のチェック状況を確認
gh pr checks 123

# PR の diff を確認
gh pr diff 123
```

## なぜ良いのか

### シンプルさ
- ブランチ戦略が単純で覚えやすい
- main ブランチと feature ブランチのみで運用可能
- 新メンバーでもすぐに理解できる

### 安全性
- main ブランチを常にデプロイ可能な状態に保てる
- すべての変更がレビューを経る
- CI/CD との統合が容易

### 透明性
- 変更履歴が明確に残る
- PR でコードレビューや議論ができる
- チーム全体で進捗を把握しやすい

### 柔軟性
- 小さな変更も大きな変更も同じフローで対応
- 緊急修正も通常の開発と同じ流れで処理
- リモートワークとの相性が良い

## Git Flow との違い

| 項目 | GitHub Flow | Git Flow |
|------|-------------|----------|
| ブランチ数 | 少ない（main + feature） | 多い（main, develop, feature, release, hotfix） |
| 複雑さ | シンプル | 複雑 |
| リリース | 継続的デプロイメント向き | 定期リリース向き |
| 学習コスト | 低い | 高い |
| 適用場面 | Web アプリケーション、SaaS | パッケージソフトウェア |

## ベストプラクティス

1. **main ブランチは常にデプロイ可能に保つ**
   - テストが通っていないコードはマージしない
   - CI/CD パイプラインを設定する

2. **小さく頻繁にコミットする**
   - 大きな変更は複数の PR に分割
   - レビューしやすい単位で PR を作成

3. **わかりやすいブランチ名とコミットメッセージ**
   - 何をしているかが一目でわかるように
   - 将来の自分やチームメンバーのために

4. **PR テンプレートを活用**
   - 変更内容、テスト方法、チェックリストを記載
   - レビュアーが理解しやすくする

5. **自動化を活用**
   - CI でテストを自動実行
   - コードフォーマットやリントを自動チェック
   - デプロイを自動化
