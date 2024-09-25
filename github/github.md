# GitHub 関連コマンド

## GitHub アカウント名の取得

- GitHub アカウント名を取得

  ```bash
  gh config get -h github.com user
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
