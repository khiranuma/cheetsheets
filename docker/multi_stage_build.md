# マルチステージビルド

## 主な目的

1. コンパイル環境と実行環境を異なるコンテナで実行したい
1. 開発環境と商用環境などでコンテナの一部の定義を共通化する必要がある場合

## コンパイル環境と実行環境を異なるコンテナで実行したい場合

C 言語や Go 言語のようにコンパイルが必要な言語をコンテナ上で実行する場合、コンパイラはコンパイル時には必要になるが実行環境には必要ない。  
このような場合、１つの Dockerfile でコンパイル環境用のコンテナと実行環境のコンテナを定義し実行環境にはコンパイラなどの不要なレイヤが含まれないように定義できる
※実行後に削除したとしてもすでに下層のレイヤに含まれているのでイメージは小さくならない。

```bash
current/
  ├ hello.c
  └ Dockerfile
```

Dockerfile

```dockerfile
# compile stage
FROM alpine:3.8 AS COMPILE

RUN apk add --no-cach gcc libc-dev

COPY ./hello.c /app/

WORKDIR /app

RUN gcc hello.c

# execution stage
FROM alpine:3.8

COPY --from=COMPILE /app/a.out /app/

WORKDIR /app

CMD ["./a.out"]
```

ポイントは`FROM`が二つ入っていること。１つ目はコンパイル環境用のコンテナのベースイメージで gcc などのコンパイルに必要なライブラリをインストールして`RUN gcc hello.c`でコンパイルしている  
２つ目の`FROM`は実行環境用のコンテナのベースイメージで`COPY --from=COMPILE /app/a.out /app`でコンパイル環境用のコンテナからコンパイル結果の`a.out`をコピーしてきている。  
COPY 時は`AS`句でコンパイル環境につけた別名`COMPILE`を`--from=COMPILE`で参照している。別名をつけない場合は`--from=0`のように何番目の`FROM`かを指定して参照する

```dockerfile
FROM alpine:3.8

RUN apk add --no-cach gcc libc-dev

COPY ./hello.c /app/

WORKDIR /app

RUN gcc hello.c

FROM alpine:3.8

COPY --from=0 /app/a.out /app/

WORKDIR /app

CMD ["./a.out"]
```

実行方法は変わらず、ビルドしてから Run する

```bash
docker image build -t multi-image .
docker container run --rm multi-build
```

レイヤを確認してみるとコンパイル用のコンテナに入れたコマンドがないことがわかる

```bash
$ docker image history multi-image
IMAGE          CREATED          CREATED BY                                       SIZE      COMMENT
f035adec8014   12 minutes ago   CMD ["./a.out"]                                  0B        buildkit.dockerfile.v0
<missing>      12 minutes ago   WORKDIR /app                                     0B        buildkit.dockerfile.v0
<missing>      12 minutes ago   COPY /app/a.out /app/ # buildkit                 10.6kB    buildkit.dockerfile.v0
<missing>      3 years ago      /bin/sh -c #(nop)  CMD ["/bin/sh"]               0B
<missing>      3 years ago      /bin/sh -c #(nop) ADD file:e38375b009a2e2c9b…   4.41MB
```

## 開発環境と商用環境などでコンテナの一部の定義を共通化する必要がある場合

下記のように共通部分、開発環境部分、商用環境部分を各ステージに記載する

```dockerfile
# Common
FROM alpine:3.18.4 AS base
CMD ["sh", "-c", "echo this container is for ${mode}"]

# Development
FROM base AS development
ENV mode="Dev"

# Production
FROM base AS production
ENV mode="Prod"
```

ポイントは`development`や`production`の`FROM`句で base を参照していること

ビルド時は下記のように`--target`句でステージ(development or production)を指定して実行する

```bash
docker build --target development .
docker build --target production .
```
