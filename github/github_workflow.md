# GitHub Actions のワークフロー関連

## workflow の手動実行

- `on: workflow_dispatch`の workflow を起動する

```bash
gh workflow run xxxx.yml
```

- 引数を指定する場合

```bash
gh workflow run xxxx.yml -f key=value
```

- ブランチ(タグ)を指定する場合

```bash
gh workflow run xxxx.yml -r <branch name>
```

## 実行された workflow を列挙する

```bash
gh workflow list
```
