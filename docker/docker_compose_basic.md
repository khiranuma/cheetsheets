# Docker Compose

## 概要

- 複数の Docker コンテナを定義して起動する仕組み
- yaml 形式のファイルにコンテナやネットワーク、ボリュームなどの定義を記載していく
- 複数の Docker コマンドを使用して定義していた構成を一度に実行できる

## version

```yaml
version: "3.9"
```

## services

ここまではほぼ定型文で services の下に各コンテナの定義を書いていく  
下記の例では api というコンテナを定義している

```yaml
version: "3.9"
services:
  api:
```

## container_name

デフォルトではコンテナ名は`{ディレクトリ名}_{service名}`のように設定される(下記のフォルダ構成だと current_db)

```bash
current/
  ├ api/
  │ ├ Dockerfile
  │ └ src
  │   └ app.py
  ├ db/
  └ docker-compose.yml
```

そこでわかりやすい名前を付けたい場合は以下のようにする

```yaml
version: "3"

services:
  db:
    #image: mysql:8.0.33
    build: ./db
    container_name: db-mysql
```

## build, image

Dockerfile を使わずに DockerHub にあるイメージをそのまま起動する場合は下記のように`image`句でイメージとタグを指定する

```yaml
version: "3.9"
services:
  db:
    image: mysql:8.0.33
```

Dockerfile でビルドする場合はビルドコンテキストを指定する

```bash
current/
  ├ api/
  │ ├ Dockerfile
  │ └ src
  │   └ app.py
  └ db/
```

```yaml
version: "3.9"
services:
  api:
    build: ./api
```

## ports

`docker container run`コマンドの`-p {ホスト側ポート}:{コンテナ側ポート}`オプションに該当する設定  
下記の設定だとホスト側のポート 8080 をコンテナ側のポート 8000 に対応させるポートフォワード設定になる。

```yaml
version: "3"

services:
  api:
    build: ./api
    ports:
      - 8080:8000
```

## depends_on

サービス間の依存関係を定義できる。  
下記のように書いた場合、db コンテナが起動したあとで api コンテナが起動する

```yaml
version: "3"

services:
  api:
    build: ./api
    ports:
      - 8080:8000
    depends_on:
      - db
  db:
    build: ./db
    container_name: db-mysql
    ports:
      - 3306:3306
```

## environment

コンテナ内で使用する環境変数を指定できる  
`docker container run`コマンドの`-e {キー}={値}`オプションと同様の設定

```yaml
version: "3"

services:
  db:
    #image: mysql:8.0.33
    build: ./db
    container_name: db-mysql
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=greeting_db
      - MYSQL_USER=greeting_user
      - MYSQL_PASSWORD=greeting
      - TZ="Asia/Tokyo"
```

## volumes

コンテナにボリュームやバインドマウントを定義を記載することが可能。

```yaml
version: "3"

services:
  db:
    #image: mysql:8.0.33
    build: ./db
    container_name: db-mysql
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=greeting_db
      - MYSQL_USER=greeting_user
      - MYSQL_PASSWORD=greeting
      - TZ="Asia/Tokyo"
    volumes:
      - db-storage:/var/lib/mysql
      - ./db/sql:/docker-entrypoint-initdb.d
volumes:
  db-storage:
```

- バインドマウントの場合は`- ./db/sql:/docker-entrypoint-initdb.d`のように docker-compose.yml からの相対パスで指定が可能
- ボリュームの場合はまず下記のように最上位(services と同じ階層)に volumes を定義する。

```yaml
version: "3"

services: ...
volumes:
  db-storage:
```

そのうえで`services:db:volumes`の下に`- db-storage:/var/lib/mysql`で{ボリューム名}:{コンテナ内の絶対パス}でマウントする場所を定義する

## network

デフォルトで docker-compose 上で作成されるコンテナの集まりは自動で作成された１つのブリッジネットワークに属するため、互いに通信することが可能でコンテナ名で名前解決できる。
(ネットワーク名は`{docker-composeのあるディレクトリ名}_default`)

一方で自分でネットワークを定義したり既存のネットワークにアタッチすることもできる

### ブリッジネットワークを定義

```yaml
version: "3.3"

services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.11.1
    container_name: kibana
    ...
    networks:
      - es-network
    restart: always

networks:
  es-network:
    driver: bridge
```

まずは最上位(services と同じ階層)に network を定義する

```yaml
networks:
  es-network:
    driver: bridge
```

そのうえでコンテナ定義でそのネットワークを指定する

```yaml
services:
  kibana:
    ...
    networks:
      - es-network
```

### 既存のネットワークにアタッチする

```yaml
version: "3.7"
services:
  logstash:
    image: docker.elastic.co/logstash/logstash:7.10.1
    volumes:
      - ./logstash/pipeline/:/usr/share/logstash/pipeline/
      - ./logstash/config/logstash.yml/:/usr/share/logstash/config/logstash.yml:ro
      - ./logstash/config/pipelines.yml/:/usr/share/logstash/config/piplines.yml:ro
    networks:
      - elasticsearch_es-network

networks:
  elasticsearch_es-network:
    external: true
```

まずは最上位(services と同じ階層)に network を定義する

```yaml
networks:
  elasticsearch_es-network:
    external: true
```

そのうえでコンテナ定義でそのネットワークを指定する

```yaml
version: '3.7'
services:

  logstash:
    ...
    networks:
      - elasticsearch_es-network
```

## Docker Compose 実行コマンド

```bash
docker-compose up -d  --build

docker-compose down

docker-compose logs
```

一部のコンテナだけに対して操作することも可能

```bash
docker-compose up {サービス名(ex. db, api)} -d

docker-compose exec {サービス名(ex. db, api)} bash

docker-compose logs {サービス名(ex. db, api)} -f
```

`docker-compose down`を実行した場合、docker-compose.yaml から起動されたすべてのコンテナとネットワークが削除される。

ただし、ボリュームは消えない
