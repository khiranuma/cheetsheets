# Git 関連コマンド

## ステージ

- カレントディレクトリ配下をまとめてステージ

```bash
git add .
```

- 指定ファイルをステージ

```bash
git add <filepath>
```

- ステージされたファイルを確認

```bash
git status
```

- ステージされたファイルと最終コミットの差分を確認

```bash
git diff --cached
```

## コミット

- ステージされた全ファイルをコミット

```bash
git commit -m "commit message"
```

- 空のコミットを作成する

```bash
git commit -m "commit message" --allow-empty
```

## Push

- 現在のブランチが origin にある場合

```bash
git push
```

- 現在のブランチが origin にない場合

```bash
git push origin <branch name>
```

## ブランチ操作

- 既存のブランチをチェックアウト

```bash
git switch <branch name>
```

- ブランチを作成してチェックアウト

```bash
git switch -c <new branch>
```

- ブランチを作成してチェックアウト(派生元のブランチ main を指定)

```bash
git switch -c <new branch> main
```

- 現在のブランチを確認

```bash
git branch
```

- ブランチを作成

```bash
git branch <new branch>
```
