# Dockerfile からイメージを作成する方法

- Dockerfile を記載してオリジナルのイメージを作成する方法

## イメージのビルド

```bash
docker image build [-f {Dockerfileパス}] -t {イメージ名}:{タグ} {ビルドコンテキストのディレクトリ名}
```

- タグ名は省略すると"latest"が自動で付加される

## Dockerfile RUN コマンド

- RUN {コマンド}

  ```dockerfile
  FROM ubuntu:20.04
  RUN apt -y update && apt install -y curl
  ```

## Dockerfile COPY コマンド

- COPY {コピー元パス} {コピー先パス}  
  ※追加先のパスは親ディレクトリ含め元のイメージに存在しないものでも OK。  
  ※追加元のパスは Dockerfile からの相対パス

  ```dockerfile
  FROM ubuntu:20.04
  RUN apt install -y && apt install -y curl
  COPY ./hello.txt /app/
  ```

## Dockerfile CMD コマンド

- コンテナ実行時のデフォルトコマンドを指定する
- Dockerfile 内で１回だけ指定可能。複数回指定された場合は最後の CMD だけが実行される

  ```dockerfile
  CMD ["実行ファイル名", "パラメータ1", "パラメータ2", ...]
  ```

  ```dockerfile
  FROM ubuntu:20.04
  WORKDIR /app
  CMD ["ls", "-la"]
  ```

## Dockerfile ENV コマンド

- 環境変数を設定する

  ```dockerfile
  ENV {キー1}={値} {キー1}={値}
  ```

  ```dockerfile
  FROM ubuntu:20.04
  ENV hello="Hello World!"
  CMD ["bash"]
  ```

- Dockerfile の ENV の値を実行時に上書きすることも可能

  ```bash
  docker container run -it --rm -e hello="Hello Japan!" {イメージ名}
  ```

## Dockerfile ARG コマンド

- イメージの作成時にだけ利用する変数を渡す。

  ```dockerfile
  ARG {キー}={デフォルト値}
  ```

- デフォルト値は指定しなくても OK

  ```dockerfile
  FROM ubuntu:20.04
  ARG message
  RUN echo $message > message.txt
  CMD ["cat", "message.txt"]
  ```

- 下記のように`--build-arg`オプションでビルド時に値を与える

  ```dockerfile
  docker image build --build-arg message='Hello Message' .
  ```

- これのイメージからコンテナを起動すると'Hello Message'と出力される

## ENV と ARG の違い

- ARG イメージ作成時に一時的に有効な変数。コンテナ実行時には参照できない。
- ENV イメージ作成時とコンテナ実行時のどちらでも有効な変数

## Dockerfile WORKDIR コマンド

- RUN や CMD が実行されるディレクトリパスを指定する

  ```dockerfile
  FROM ubuntu:20.04
  RUN touch 1.txt
  WORKDIR /app/my_dir
  RUN touch 2.txt
  WORKDIR ..
  RUN touch 3.txt
  CMD ["bash"]
  ```

- コンテナ実行時には`/app`で`bash`が実行される
- 実行後のディレクトリ構成は下記のようになる

  ```bash
  /
  ├ app/
  │ ├ 3.txt
  │ └ my_dir
  │   └ 2.txt
  └ 1.txt
  ```

## Dockerfile EXPOSE コマンド

```dockerfile
EXPOSE [ポート番号]
```

EXPOSE は書いただけでは動作は特に変わらない。
どのポートを公開する意図であるかをコンテナ実行者に対して意思表示するために使用する
実際にポートを公開するためには`docker container run`コマンドに`-p`オプションで指定する

```bash
docker container run -p 8080:80 {イメージ名/ID}
```

> EXPOSE 命令だけは、実際にはポートを 公開 publish しません。これは、どのポートを公開する意図なのかという、イメージの作者とコンテナ実行者の両者に対し、ある種のドキュメントとして機能します。コンテナの実行時に実際にポートを公開するには、 docker run で -p フラグを使い、公開用のポートと割り当てる（ マップ map する）ポートを指定します。  
> [公式ドキュメント](https://docs.docker.jp/engine/reference/builder.html#id46)

```bash
$ docker image inspect [イメージ名/ID]
        "Config": {
            ...
            "ExposedPorts": {
                "8000/tcp": {}
            },
        }
```

## Docker のイメージレイヤについて

コンテナイメージは読み取り専用のイメージレイヤを積み重ねて構成される。実行時には書き込み可能なコンテナレイヤがイメージレイヤの上に重ねられるイメージ。

- レイヤ

  - イメージレイヤ
  - コンテナレイヤ
    イメージレイヤも元のイメージ部分からなるベースイメージの上に Dockerfile で記載したコマンドごとにレイヤを積み重ねて作成される

- イメージのレイヤを確認する
  ```bash
  docker image history {イメージ名}
  ```
- 実行結果

  ```bash
  $ docker image history my-image
  IMAGE          CREATED       CREATED BY                                       SIZE      COMMENT
  44d8fc74e0ec   3 days ago    COPY hello.txt /app/ # buildkit                  13B       buildkit.dockerfile.v0
  <missing>      3 days ago    RUN /bin/sh -c apt -y update && apt install …   62.5MB    buildkit.dockerfile.v0
  <missing>      8 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]             0B
  <missing>      8 weeks ago   /bin/sh -c #(nop) ADD file:233702cd816c07bc9…   72.8MB
  <missing>      8 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
  <missing>      8 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
  <missing>      8 weeks ago   /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH      0B
  <missing>      8 weeks ago   /bin/sh -c #(nop)  ARG RELEASE                   0B
  ```

- レイヤを少なくすることでイメージ自体を小さくすることができる
  下記のような場合、ベースレイヤに３つのレイヤが積み重なることになる。

  ```dockerfile
  RUN apt -y update
  RUN apt -y install curl
  RUN apt -y install vim
  ```

- これを下記のように書くことで１つのレイヤで済むことになる

  ```dockerfile
  RUN apt -y update && apt install -y curl vim
  ```

- 一方で各レイヤはキャッシュされるので共通部分を先に処理することでキャッシュが効いてビルドが早くなる

  ```dockerfile
  FROM ubuntu:20.04
  RUN apt -y update
  RUN apt -y install curl
  RUN apt -y install vim
  CMD ["bash"]
  ```

- 上の Dockerfile をビルドした後に下記をビルドすると curl のインストールまではキャッシュが使われるので再実行されない  
  しかし、それ以降に書かれた CMD コマンドは同じでも再度実行される。

  ```dockerfile
  FROM ubuntu:20.04
  RUN apt -y update
  RUN apt -y install curl
  CMD ["bash"]
  ```

- キャッシュとレイヤを少なくすることはトレードオフになることもあるのでよく理解して検討する。

## ビルドコンテキストについて

- `docker image build`コマンドで最後に指定しているパス。"."を指定することが多い気がするけど。

  ```bash
  docker image build -t {イメージ名}:{タグ名} {ビルドコンテキスト}
  ```

- 現時点での理解では下記のような感じ

  - ビルドコンテキストで指定されたディレクトリ以下のファイルは(.dockerignore に記載されていない場合)REST API を介して Docker daemon に送信される

### ビルドコンテキストに関連して注意すべきこと

- 下記のようなディレクトリ構成を考える

  ```bash
  current/
  　├ docker/
  　│　└ Dockerfile
  　└ hello.txt
  ```

- Dockerfile の内容が下記の場合

  ```dockerfile
  FROM ubuntu:20.04
  COPY ../hello.txt /app
  ```

- `current/docker`で下記のように実行するとエラーになる

  ```bash
  docker image build -t test-image .
  ```

- 理由はビルドコンテキストとして`.`つまり`current/docker`以下のディレクトリだけが送信されているため、`current/hello.txt`は Docker daemon からは見えない。

- この構成で正しくビルドしてやるには下記のようにすればいい

  ```bash
  $ cd ../
  $ pwd
  current/
  $ docker image build -f docker/Dockerfile -t test-image:v1 .
  ```

- Dockerfile として docker 配下の Dockerfile を指定し、ビルドコンテキストは`current/`配下をすべて転送する

## .dockerignore

- 下記のようなディレクトリ構成を考える

  ```bash
  current/
  ├ app/
  │ ├ settings.conf
  │ ├ app.py
  │ └ large.txt
  └ Dockerfile
  ```

- 下記のように Dockerfile を書いた場合、大きなファイルである large.txt をイメージに含めるとイメージが大きくなってしまう

  ```dockerfile
  FROM ubuntu:20.04
  COPY ./app /app/
  ```

- このような場合に下記の内容で`current/.dockerignore`ファイルを作成する

  ```.dockerignore
  app/large.txt
  ```

- .dockerignore ファイルに書かれた内容は REST API によって Docker daemon に渡されないため、イメージに含まれないかつ転送されないのでイメージ作成にかかる時間も短縮される。
