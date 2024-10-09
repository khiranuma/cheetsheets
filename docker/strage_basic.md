# Docker のストレージについての基礎

## レイヤ

- イメージレイヤ 読み取り専用
- コンテナレイヤ 書き込み可能(エフェメラル)

## データ永続化する方法

1. ボリューム
   - 同じホスト OS 内の複数のコンテナ間でファイルの共有ができる
   - ホスト OS との共有はできないこともないが、その目的がある場合はバインドマウントを使う
   - ホスト OS の/var/lib/docker/volumes/\*\*あたりにできる(詳しくは`docker volume inspect`コマンドで確認できる)
1. バインドマウント
   - 同じホスト OS 内の複数のコンテナ間でのファイル共有ができる
   - ホスト OS の指定したディレクトリを共有できる
   - ホスト OS でファイルを編集しながらコンテナ環境で実行したなどの場合に便利

## ボリュームを使う

### ボリューム操作の基本コマンド

```bash
docker volume create {ボリューム名}

docker volume ls

docker volume inspect {ボリューム名}

docker volume rm {ボリューム名}
```

### コンテナにマウントする

```bash
docker container run -v {ボリューム名}:{コンテナ内の絶対パス}:{モード} {イメージ名/ID}

docker container run --mount type=volume,src={ボリューム名},dst={コンテナ内の絶対パス} {イメージ名/ID}
```

`-v`と`--mount`は書き方の違いだけで結果は同じ

## バインドマウントを使う

- ホスト OS 上の指定したディレクトリをコンテナにマウントできる

```bash
docker container run -v {ホスト側の絶対パス}:{コンテナ側の絶対パス}:{モード} {イメージ名/ID}
docker container run --mount type=bind,src={ホスト側の絶対パス},dst={コンテナ側の絶対パス} {イメージ名/ID}
```

`-v`と`--mount`は書き方の違いだけで結果は同じ

ちなみに Windows などでホストのパスを絶対パスで記述するのは面倒なので下記のように環境変数を利用すると便利

```bash
docker container run --rm -it -v ${pwd}/docker:/app ubuntu:20.04
```

## 使い分け

基本はホスト OS から編集したいかどうかだけだと思われる
