
# docker compose solr-cloud

SolrCloud環境をローカルで実験するためのプロジェクトです。

起動するdockerコンテナは下記の数になります。
* zookeeper x3
* solr (upconfig専用) x1
* solr (SolrCloud構成) x4

solrは下記の構成になります。
* shard x4
* replica x2

## version

起動するMWは下記のバージョンです。

- java (hotspot) :
    - `1.8.0_144`
- zookeeper :
    - `3.4.6`
- solr :
    - `6.5.1`

## zookeeper

前回起動時に作成されたファイルを消しておきます。

```bash
rm -r zookeeper/zookeeper-1/data/version-2/
rm -r zookeeper/zookeeper-2/data/version-2/
rm -r zookeeper/zookeeper-3/data/version-2/
```

実験で使いたいsolr設定ファイルを `tmp/configs/sample-conf-1/` に配置します。(プロジェクトに応じて用意します)
```bash
cp -r SOME-CONFIGS/ tmp/configs/sample-conf-1/
```

zookeeperを起動します。

```bash
### 初回や、dockerfileの修正時にビルド
docker-compose -f docker-compose-zookeeper.yml build
### 起動
docker-compose -f docker-compose-zookeeper.yml up -d
docker ps -a
```

solrの各種設定ファイルをzookeeperにアップロードします。
下記コマンドでは、 `zookeeper-1` に対してアップロードしていますが、他の `zookeeper-2` , `zookeeper-3` でも問題ないです。

```bash
docker exec -it solr-for-upconfig /bin/bash
/opt/solr/current/server/scripts/cloud-scripts/zkcli.sh -cmd upconfig -zkhost zookeeper-1:2181 -confdir /opt/solr/configs/sample-conf-1 -confname sample-conf-1
```

### 説明

* `-confdir` に指定している `/opt/solr/configs/sample-conf-1` は、このプロジェクトの `tmp/configs/sample-conf-1` 配下をdockerコンテナ上にマウントしたディレクトリになります。
    * `tmp/configs/sample-conf-1` --> `/opt/solr/configs/sample-conf-1`
    * プロジェクトごとに試したいsolrの設定は変わってくると思うので、 `tmp/configs/HOGEHOGE-CONF` のようにconfigs以下に並べて配置するとわかりやすくて良いです。
    * `tmp/` はgitignoreでgit管理対象外にしています。
* `-confname` に指定している `sample-conf-1` は、後述のcollection作成の際に指定するconfigNameです。
* dockerコンテナ `solr-for-upconfig` はupconfigする用途で起動しているだけなので、用が済んだら停止しても問題ありません。

### 補足

zookeeperに接続し、アップされているファイルを確認する。

zookeeperプロンプトに接続

```bash
docker exec -it zookeeper-1 /bin/bash
/opt/zookeeper/current/bin/zkCli.sh -server zookeeper-2:2181
```

zookeeperプロンプト上での操作

```bash
ls /configs/sample-conf-1
```

## solr

solrを起動します。

```bash
### 初回や、dockerfileの修正時にビルド
docker-compose -f docker-compose-solr.yml build
### 起動
docker-compose -f docker-compose-solr.yml up -d
docker ps -a
```

collection作成します。

```bash
docker exec -it solr-1 /bin/bash
/opt/solr/current/bin/solr create_collection -c sample1 -n sample-conf-1 -shards 4 -replicationFactor 2 -p 8983 -force
```

### 説明

* `-c sample` でcollection名をsampleに指定しています。
* `-n sampel-conf-1` でzookeeperにアップされている `sample-conf-1` 設定を指定しています。
* `-force` でsolrユーザ以外(ここではroot)で起動するときの警告を無視しています。

### Solr管理コンソール

下記URLから色々叩けます。

* http://localhost:18983/solr/#/~cloud
* http://localhost:18984/solr/#/~cloud
* http://localhost:18985/solr/#/~cloud
* http://localhost:18986/solr/#/~cloud

### collection削除

データを丸ごと削除してやり直したい時に手っ取り早い方法。

```bash
curl -XGET 'http://localhost:18983/solr/admin/collections?action=DELETE&name=sample'
```

