# GitHub 関連コマンド

## リポジトリを作成する

- プライベートリポジトリを作成してクローンする

```bash
gh repo create <repo name> --private --clone --add-readme
```

- パブリックリポジトリを作成してクローンする

```bash
gh repo create <repo name> --public --clone -add-readme
```

## GitHub アカウント名の取得

- GitHub アカウント名を取得

  ```bash
  gh config get -h github.com user
  ```

## GitHub CLI に許可されている権限を調べる

- 許可されている権限を調べる
  ```bash
  gh auth status
  ```

## Github CLI にクレデンシャルを追加する

- GitHub CLI に GitHub Packages へのアクセス権限を追加

  ```bash
  gh auth refresh --scopes write:packages
  ```

  - コマンド実行後コンソールに`one-time code`が表示され、`Enter`キーを押すとブラウザが開くのでその画面にコンソールに表示された`one-time code`を入力して権限の追加を許可する

## GitHub CLI の一時クレデンシャルの取得

- token の取得

  ```bash
  gh auth token
  ```

- token を使用して GitHub Package のコンテナレジストリにログイン
  ```bash
  gh auth token | docker login ghcr.io -u <account id> --password-stdin
  ```
