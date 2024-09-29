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

## workflow を列挙する

- workflow のリストを取得する

  ```bash
  gh workflow list
  ```

- workflow の詳細を確認する(実行履歴含む)
  - run_id を確認するのに有用
  ```bash
  gh workflow view
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
