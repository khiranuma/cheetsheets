# Pull Request 関連コマンド

## Pull Request の確認

- Pull Request の内容を見る

```bash
gh pr view
```

## Pull Request の作成

- タイトルと本文を指定して Pull Request の作成

```bash
gh pr create --title "Super Useful Feature" --body "This is very useful"
```

- ラベルを付けて Pull Request の作成

```bash
gh pr create --title "Super Useful Feature" --body "This is very useful" --label "enhancement"
```

- 最初のコミットからタイトルを引用して Pull Request の作成

```bash
gh pr create --fill-first
```

## Pull Request の編集

- ラベルの追加

```bash
gh pr edit --add-label "enhancement"
```

- ラベルの削除

```bash
gh pr edit --remove-label "enhancement"
```

## Pull Request のマージ

- マージコミットを作成

```bash
gh pr merge --merge
```

- ベースブランチにリベース

```bash
gh pr merge --rebase
```

- コミットを一つにまとめてベースブランチにマージ

```bash
gh pr merge --squash
```
