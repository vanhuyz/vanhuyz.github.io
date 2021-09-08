+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-01-16T08:37:00Z
description = ""
draft = false
slug = "try-magical-docker"
tags = ["TIL", "Japanese"]
title = "魔法のDockerを使ってみよう"

+++


[前回](https://galapagos.qiita.com/vanhuyz/items/cf1badda97a0de645db3)はDockerをインストールするまで記述しました。今回は実際Dockerで構築したRailsアプリを動かしましょう。

## 前提
 Mac OS Xにboot2dockerが起動された状態、またはLinuxにDockerインストール済み

## 作業
* まず[Fig](http://www.fig.sh/index.html)というコンテナ管理ツールをインストールします

```
$ curl -L https://github.com/docker/fig/releases/download/1.0.1/fig-`uname -s`-`uname -m` > /usr/local/bin/fig; chmod +x /usr/local/bin/fig
```

* 次に構築したRailsアプリをcloneします

```
$ git clone git@github.com:vanhuyz/rails-docker-test.git
$ cd rails-docker-test
```

* 最後に`fig up` するだけで

```
$ fig up
```

完了!

## 構築したRailsアプリの確認　（Yosemite編）
僕のRails環境は以下のように構成されています

* Ruby 2.2
* Rails 4.2.0
* MySQL
* Redis

では構築できた環境を確認しましょう！

まず`boot2docker`仮想マシンのIPを確認します。

```
Vans-MacBook-Air:~ vanhuy$ boot2docker ip
192.168.59.103
```

webコンテナは3000ポートに紐付いているのでブラウザから`192.168.59.103:3000`にアクスセスしてみると

![Screen Shot 2015-01-16 at 1.40.47 PM.png](https://qiita-image-store.s3.amazonaws.com/1758/60017/20a835ce-c004-7567-0ab5-5bed5697a058.png)

やった！ちゃんとRailsの初期画面が出ています。

次に`mysql`の確認：

```
Vans-MacBook-Air:~ vanhuy$ mysql -h 192.168.59.103 -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.22 MySQL Community Server (GPL)
...
mysql>
```

これも動きますね。rootパスワードは`fig.yml`の中に書いています。実際は環境変数に保存するとよいでしょう。

最後は`redis`の確認：

```
Vans-MacBook-Air:~ vanhuy$ redis-cli -h 192.168.59.103
192.168.59.103:6379>
```

これも問題ないです。
大成功！

## 感想

* Dockerのコードが短い。今回のコードは`fig.yml`と`Dockerfile`しかのっていなく、合計30行ぐらい
* Chefを忘れてもいいかも
* Dockerは面白くて、いろんなパターンがあるから、興味がある方はぜひコメントしてください！
* Dockerは社内に普及できるといいな！
* 次回はどうやってこのRailsアプリを構築したのか、手順とコードを説明しようと思います。

