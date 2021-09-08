+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-01-20T11:10:00Z
description = ""
draft = false
slug = "use-swagger-ui-in-docker"
tags = ["TIL", "Japanese"]
title = "Docker経由Swagger UIを入れてみた"

+++


## はじめに

* 目的：SwaggerUIを開発環境に入れて、Railsと組み合わせたい
* Railsのswagger-ui gemもありますが、あれはSwaggerUIが古い。最新版のSwaggerUIを使いたい。

手動で構築するのがちょっと面倒だから、Dockerを使って入れてみた

##  作業

Docker HubにSwagger UIを探してこういうイメージを見つけた

https://registry.hub.docker.com/u/capoferro/swagger-ui/
> capoferro / swagger-ui
Simple docker container to serve the Swagger UI 

インストールする方法は以下となります

* イーメジをpullする

```bash
$ docker pull capoferro/swagger-ui
```

* コンテナを起動する

```bash
$ docker run -d -p 4567:80 capoferro/swagger-ui
```

このコマンドはSwaggerUIコンテナの80ポートをホストマシンの4567ポートに紐付ける

* ブラウザに`boot2dockerマシンのIP`（普通192.168.59.103）のポート4567をアクセスするとSwaggerの画面が出てくる

![Screen Shot 2015-01-20 at 7.31.02 PM.png](https://qiita-image-store.s3.amazonaws.com/1758/60017/673d2c95-380f-8879-8b5f-5d165a87fd6c.png)

これでSwaggerUIのインストールが成功した。

最後にRailsのアプリを紐付けること:

* 動いているSwaggerコンテナのIDを確認する ( `$ docker ps`)

![Screen Shot 2015-01-20 at 7.35.24 PM.png](https://qiita-image-store.s3.amazonaws.com/1758/60017/21a86082-e19e-15e7-6851-2775a415ed38.png)

今回コンテナIDは`859502ad6704`がわかりました。

* SwaggerUIコンテナに入る

```bash
$ docker exec -i -t 8595 /bin/bash
``` 
 
(8595はコンテナIDの先頭４文字です。コンテナIDを指定するとき先頭3,4文字でも動くはず）

* `index.html`の中に`url`のところにデフォルトURLをRailsのswagger_docパスに変更する： <br>
`vi`でもいいですが、キーボードがおかしかったので`sed`を使いました

```bash
$ sed -i "s@http://petstore.swagger.wordnik.com/api/api-docs@http://192.168.59.103:3000/api/swagger_doc@g" index.html
```

終わり！

## まわりの話

SwaggerUIイメージのソースコード: https://github.com/capoferro/swagger-ui-docker

`Dockerfile`をみてわかるように、このイメージはUbuntuベース、nginx上にSwaggerUI動くという形です。SwaggerUIは公式のgithubをプルするので最新版になっているだろう。

