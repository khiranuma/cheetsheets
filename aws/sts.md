## CLI 実行者の認証情報を取得する

- アカウント ID、IAM ユーザ、IAM ロールの取得

```bash
aws sts get-caller-identity
```

- 個別の項目(ex.アカウント ID)だけを取得する

```bash
aws sts get-caller-identity --query Account --output text
```

## Assume Role
