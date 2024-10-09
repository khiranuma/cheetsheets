# Docker のネットワークについて基礎

## ポートフォワードについて

Docker のネットワークとは直接は関連しないが、コンテナ内のサービスのポートにホスト OS のポートを対応させる

```bash
docker container run -p {ホスト側ポート}:{コンテナ側のポート} {イメージ名/ID}
```

## コンテナ間のネットワーク

App コンテナと DB コンテナ間での通信や逆に不要なコンテナ間で通信させないなどのためにネットワークの構成が必要。

## デフォルトのネットワーク

Docker のデフォルトのネットワークが３つ定義されている

```bash
docker network ls
```

```
NETWORK ID     NAME      DRIVER    SCOPE
c03a3af8045e   bridge    bridge    local
1e1fcc8b83fb   host      host      local
0168d70975df   none      null      local
```

`host`, `none`についてはいつか調べることにして、基本的にネットワークを指定せずにコンテナを起動すると`NAME bridge`というネットワークに接続される
コンテナを適当に起動してみる

```bash
docker container run --rm --name my-cotainer -itd ubuntu:20.04
```

bridge ネットワークに対してどのコンテナが接続されているかを確認する

```bash
docker network inspect {ネットワーク名}
```

実行結果の一部を抜粋

```
        "Containers": {
            "81f35c04c027a0ee8aa2c5d58d41710b718f3df4c080c93f66e7db4c44760eb4": {
                "Name": "my-cotainer",
                "EndpointID": "7775e0bb3b44a081150a2a6ca80799c4faaaae257d55dd07b3c5cbb913259492",
                "MacAddress": "......",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
```

このように NAME bridge のネットワークに接続されたことがわかる

## ブリッジネットワーク

- 同じホスト内のコンテナ同士を接続するためのネットワーク
- コンテナが同じブリッジネットワークに属していれば互いに通信ができ、属していなければ通信できない
- コンテナ名をつかって名前解決ができる(ただしデフォルトの NAME bridge のブリッジネットワーク内ではコンテナ名による名前解決はできない。IP アドレスによる通信はできる)
  - ex. curl http://my-container

### ブリッジネットワークを作成する

```bash
docker network create {ネットワーク名}

docker network ls

docker network inspect {ネットワーク名}

docker network rm {ネットワーク名}
```

※`docker network rm`は対象のネットワークに属するコンテナがあると削除できないので先にコンテナを削除する必要がある
