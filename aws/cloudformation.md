## Template の検証

- template の検証

```bash
aws cloudformation validate-template --template-body file://01_xxxxx.yml
```

## スタックの作成

- stack のデプロイ

```bash
aws cloudformation deploy --stack-name <stack name> --template-file .\01_xxxxx.yml
```

-- stack のデプロイ(パラメータ指定)

```bash
aws cloudformation deploy --stack-name <stack name> --template-file XXXXX.yml --parameter-overrides key1=value1 [key2=value2 ...]
```

-- stack のデプロイ(IAM リリースを作成する)

```bash
aws cloudformation deploy --stack-name <stack name> --template-file XXXXXX.yml  --capabilities CAPABILITY_IAM
```

-- stack のデプロイ(名前付きの IAM リリースを作成する)

```bash
aws cloudformation deploy --stack-name <stack name> --template-file XXXXXX.yml  --capabilities CAPABILITY_NAMED_IAM
```

## スタックの削除

- スタックの削除

```bash
aws cloudformtion delete-stack --stack-name <stack name>
```

## スタックの一覧

- スタックの一覧(削除済み含む)

```bash
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE
```

- 複数の状態について一覧

```bash
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE CREATE_FAILED CREATE_COMPLETE
```

- StackName と StackStatus のみ取得

```bash
aws cloudformation list-stacks --query "StackSummaries[*].[StackName, StackStatus]"
```

## スタックイベントの一覧

```bash
aws cloudformation describe-stack-events --stack-name <stack name>
```
