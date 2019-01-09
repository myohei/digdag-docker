digdag on docker
-------------------------------

digdag in docker.
digdagのワークフローもdockerで動かしたほうが安心です。rubyとかpythonとか確認してない。
~~zeusですでにdigdagが動いており、ポートの設定周りでファイルをわけてあります。~~


## ローカル

```bash
$ docker-compose -f docker-compose.yml -f docker-compose.local.yml up --build
```

テストで確認するには、

```bash 
$ digdag sessions
```

で動くのを確認したり、[digdag-workflows](https://github.com/Growth-Hack-Studio/digdag-workflows)のサンプルをPushしてみたり。

```bash
$ cd ${digdag-workflows}/test-docker
$ digdag push hoge 
$ digdag sessions
```

## 本番

~~`-f` で `docker-compose.prod.yml` を指定する。 ポートが`65433`なのをお気をつけて。
zeusですでにdigdag severが動いてるのでこの処置をしてます。~~

```bash
$ docker-compose -f docker-compose.yml up --build -d 
```

クライアントは同じサーバーの場合は

```bash
$ digdag session -e http://127.0.0.1:65432
```

とかで確認できます。 `-e`オプションがエンドポイント指してます。
