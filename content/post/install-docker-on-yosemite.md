+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-01-15T07:59:00Z
description = ""
draft = false
slug = "install-docker-on-yosemite"
tags = ["TIL", "Japanese"]
title = "Dockerをインストールする（Yosemite編）"

+++


# Docker導入する理由
* 開発環境を一気構築できる
* コンテナ型仮想化なので軽い
* Vagrant/Chefより学習コストが低い
* 小さいコンテナから構成されるので安定性が高まる
* [Amazon EC2 Container Service](http://aws.amazon.com/ecs/)が発表されたから本番構築でも可能に！


## Yosemiteにインストールしてみよう
インストール方法は非常に簡単です。
 
* [Boot2docker](https://github.com/boot2docker/osx-installer/releases)パッケージをダウンロードする
* ダウンロードしたパッケージをダブルクリックしてインストールする 
* Boot2Dockerアプリを起動する
* どのターミナルにも使いたい場合は以下のコマンドを`~/.bash_profile`に追加する

```
 $(boot2docker shellinit)
```

終わり！
参考：[公式サイト](https://docs.docker.com/installation/mac/)

## Boot2dockerを分解してみよう
ではBoot2dockerは何が入っているのかを調べてみた。

* `VirtualBox`：仮装化ソフトウェア
* `Boot2Docker`：小さいLinuxベースOS。DockerはLinuxベースで動作するから、Macに動作する場合は仮装マシンが必要。インストールできたら`boot2docker-vm`がVirtualBoxに入っているはず 
* `Boot2Docker Management Tool`： Boot2Dockerの管理ツール。コマンドが充実。

```
$ boot2docker
Usage: boot2docker [<options>] {help|init|up|ssh|save|down|poweroff|reset|restart|config|status|info|ip|shellinit|delete|download|upgrade|version} [<args>]

$ boot2docker ssh # boot2docker仮装マシンにSSHする
$ cat /proc/version
Linux version 3.16.7-tinycore64 (root@064f0e1ce709) (gcc version 4.7.2 (Debian 4.7.2-5) ) #1 SMP Tue Dec 16 23:03:39 UTC 2014
```

* `Docker Client`：`docker`コマンドが使えるようになります。Dockerの使い方は本当に長くなりそうですから、以下は重要なコマンドをリストアップします

```
$ docker                                     # 全部コマンドが見られる
$ docker version                             # dockerバージョンを見る
$ docker search <string>                     # DockerHubにイメージを検索
$ docker pull <username>/<repository>        # イメージをダウンロード 
$ docker run <username>/<repository> <command> 
## コンテナの中にコマンドを実行する。例えば： $docker run learn/tutorial echo “hello”

$ docker ps -l                               # 作ったコンテナのIDを調べる
$ docker commit <ID> <username>/<new_repository>
## コンテナをイメージ化する

$ docker inspect <ID>                        #  コンテナの全部情報が見られる
$ docker images                              #  現在のホストになんのイメージが入っているのか
$ docker push <username>/<repository>        #  DockerHubにpushする
```     

参考：[Try Docker](https://www.docker.com/tryit/) 
## まとめ
Dockerのインストール方法と基本コマンドを紹介しました。興味ある方はDockerの[基本概念](https://docs.docker.com/introduction/understanding-docker/) （[image](https://docs.docker.com/terms/image/), [container](https://docs.docker.com/terms/container/), [docker hub](https://docs.docker.com/terms/registry/)など）を一度目を通しましょう！

次回はDockerを使ってRailsアプリの初期化する方法を紹介しようと思います。

![automate-all-the-things.jpg](https://qiita-image-store.s3.amazonaws.com/1758/60017/0566a8e9-3ca5-9443-8ba9-437946e4d98b.jpeg)

