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

## リポジトリに Secrets を設定する

- Secrets の設定

  ```bash
  gh secret set SECRET_NAME --body "SuperSecretValue"
  ```

- 登録されている Secrets のリストを取得する(値は取れない)
  ```bash
  gh secret list
  ```
