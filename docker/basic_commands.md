# イメージとコンテナのライフサイクル

- イメージ
  - レジストリから取得
    - ex.DockerHub, プライベートレジストリ
  - Dockerfile から作成

# イメージ系のコマンド

- Docker レジストリからイメージを取得する

  ```bash
  docker image pull {イメージ名}
  ```

- ローカルにあるイメージを一覧する
  ```bash
  docker image ls
  ```
- ローカルのイメージを削除する
  ```bash
  docker image rm {イメージ名/ID}
  ```

# コンテナ系のコマンド

- コンテナを実行する
  ```
  docker container run {イメージ名/ID}
  ```
- ローカルのコンテナ一覧を取得する
  ```bash
  docker container ls [-a]
  ```

※ -a オプションをつけることで status が Exited になっているコンテナも表示できる

- コンテナを停止する
  ```bash
  docker container stop {コンテナ名/ID}
  ```
- コンテナを再起動する
  ```bash
  docker container restart {コンテナ名/ID}
  ```
- 停止中のコンテナを削除する
  ```bash
  docker container rm {コンテナ名/ID}
  ```

**コンテナのログを確認する**

- 標準出力を表示

  ```bash
  docker container logs {コンテナ名/ID}
  ```

# インタラクティブモードでコンテナを実行する

```bash
docker container run --it {イメージ名/ID}
```

※ -i オプションで標準入力を開く
※ -t がないと tty がないのできれいに表示できない

# Docker image のデフォルトコマンドを調べる

```bash
docker image inspect {イメージ名/ID}
```

```bash
		        "Config": {
		           …
		            "Cmd": [
		                "/bin/bash"
            ],
```

# デフォルトのコマンドを実行時に上書きする

```bash
docker container run {イメージ名/ID} {実行コマンド}
```

# 実行中のコンテナに対して任意のコマンドを実行する

```bash
docker container exec {コンテナ名/ID} {コマンド}
```

実行中のコンテナに bash で接続する(bash がないイメージの場合 sh などを使う)

```bash
docker container exec -it {コンテナ名/ID} /bin/bash
```

※ -it をつけて bash をインタラクティブに実行する

# コンテナやイメージを整理する

散らかってきたコンテナ環境を整理しやすくするために役に立つコマンド

## コンテナにわかりやすい名前を付ける

```bash
docker container run --name {コンテナ名} {イメージ名/ID}
```

## 停止済みのコンテナを一括削除

```bash
docker container prune
```

## 実行後に停止と同時に削除されるコンテナを起動する

```bash
docker container run --rm {イメージ名/ID}
```

## 起動中のコンテナを破棄する(stop + rm)

```bash
docker container rm -f {コンテナ名/ID}
```

# コンテナをデタッチモードで起動する

```bash
docker container run -d {コンテナ名/ID}
```

コンテナにアタッチする

```bash
docker container attach {コンテナ名/ID}
```

デタッチする
Ctrl + p , Ctrl + q
