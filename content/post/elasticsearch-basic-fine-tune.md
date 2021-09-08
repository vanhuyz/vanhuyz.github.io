+++
author = "Huy Van"
categories = ["Japanese", "Elasticsearch"]
date = 2017-04-29T11:19:24Z
description = ""
draft = true
slug = "elasticsearch-basic-fine-tune"
tags = ["Japanese", "Elasticsearch"]
title = "Elasticsearchチューニング作業"

+++


##  現在の情報を確認

```bash
$ curl -XGET 'http://10.0.15.74:9200/_nodes/stats'
```

##  設定変更

公式ドキュメントに沿って作業を進めます。
### heap sizeを変更する[^1]
ベストプラクティスはheap sizeをメモリの半分にすればよいです。但し、32GB以上に設定しないとのことです。

デフォルト値の確認：

```json
"mem":{  
    "heap_used_in_bytes":109944768,
    "heap_used_percent":10,
    "heap_committed_in_bytes":259719168, // 256MB
    "heap_max_in_bytes":1065025536,      // 1GB
    "non_heap_used_in_bytes":87770408,
    "non_heap_committed_in_bytes":89821184,
```

EC2 t2.smallのメモリは2GBなので1GBに設定します。

```diff:/etc/default/elasticsearch
##  Heap size defaults to 256m min, 1g max
##  Set ES_HEAP_SIZE to 50% of available RAM, but no more than 31g
- #ES_HEAP_SIZE=2g
+ ES_HEAP_SIZE=1g
```

`sudo service elasticsearch restart`して再度確認：

```json
"mem":{
    "heap_used_in_bytes":29761384,
    "heap_used_percent":2,
    "heap_committed_in_bytes":1065025536,
    "heap_max_in_bytes":1065025536,
    "non_heap_used_in_bytes":55744888,
    "non_heap_committed_in_bytes":56827904,
...
```

`heap_committed_in_bytes`と`heap_max_in_bytes`両方1GBになりました。両方同じ値は理想だそうです。


### swapを無効化

swap領域を使う状態になったら死ぬと同等なので、swapを無効化します。
完全に無効化でもいいですが、`swappiness`を1にすれば、緊急状態だけswapを使うように設定します。

```diff:/etc/sysctl.conf
+vm.swappiness = 1
```

### File DescriptorsとMMapの設定[^2]

```json
"process":{
    "timestamp":1468491592059,
    "open_file_descriptors":154,
    "max_file_descriptors":65535,
...
```

デフォルトは1000が多いですが今回は65535でしたので特に変更が必要ないです。望ましいのは64000です。
max_map_count[^3]はデフォルト65536なのでこれも上げます。

```diff:/etｍc/sysctl.conf
+vm.max_map_count = 262144
```

`sysctl`に関する設定はインスタンスを再起動しないと反映しないので注意が必要です。

##  Elasticsearchの監視ツールMarvelを設定
バージョン合わせるのが大変で、とりあえずVagrantのElasticsearch, Kibana全部最新バージョン上げました。
バージョン上げると全部のプラグインが使えなくなるので再インストールする必要です（汗）。
本番はどうやるべきか検討が必要です。

```bash
$ cd /usr/share/elasticsearch
$ sudo bin/plugin install license
$ sudo bin/plugin install marvel-agent

$ cd /opt/kibana
$ sudo bin/kibana plugin --install elasticsearch/marvel/latest
```

* 無料licenseを取得：
この[ページ](https://www.elastic.co/guide/en/marvel/current/license-management.html)に従ってやっています。取得したjsonを`elasticsearch_license.json`に変名し、サーバーにscpします。それから`curl`コマンドでライセンスを更新します。


```bash
$ curl -XPUT 'http://localhost:9200/_license?acknowledge=true' -d @elasticsearch_license.json
```


インストール終わったらElasticsearchとKibanaを再起動します。

```bash
$ sudo service elasticsearch restart
$ sudo service kibana restart
```

Kibanaの画面で確認：

<img width="1614" alt="Screen Shot 2016-07-15 at 7.04.24 PM.png" src="https://galapagos.qiita.com/files/65cebf3b-a181-be09-13c3-bfc91384175f.png">


[^1]: [Elasticsearch Doc] [Heap: Sizing and Swapping](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#heap-sizing)
[^2]: [Elasticsearch Doc] [File Descriptors and MMap](https://www.elastic.co/guide/en/elasticsearch/guide/current/_file_descriptors_and_mmap.html)
[^3]: [max_ map_ countとは](http://stackoverflow.com/a/11685165/4314737)

