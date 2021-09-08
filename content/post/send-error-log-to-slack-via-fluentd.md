+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-05-21T09:24:00Z
description = ""
draft = false
slug = "send-error-log-to-slack-via-fluentd"
tags = ["TIL", "Japanese"]
title = "Fluentd導入してからエラーログを全部Slackに通知する"

+++


## やりたいこと

サーバーが落ちるときすぐ管理者に通知する

#### 設計イメージ
![nginx_rails_fluentd_slack.jpg](https://qiita-image-store.s3.amazonaws.com/1758/60017/d5314662-51da-9291-a3be-1a8616f32b1b.jpeg)

Fluentdで全部のエラーログをまとめてSlackに送信する。
## Fluentd導入
### Fluentdとは
ログ収集ミドルウェアである。Fluentdにインプットされた、すべてのログをJSONに変換し、アウトプットする。

### Fluentdのインストール　(Ubuntu 14.04)
#### 事前準備

* ntpをインストールする

```bash
$ sudo apt-get install ntp
```

* ファイルディスクリプタの上限を増やす

現在のファイルディスクリプタ数を確認

```bash
$ ulimit -n
1024
```
もし1024なら不十分なので上限を増やす。`/etc/security/limits.conf `に以下の行を追加する

```
root soft nofile 65536
root hard nofile 65536

* soft nofile 65536
* hard nofile 65536
```

編集終わったらマシン再起動

```bash
$ sudo reboot
```

もう一度確認

```bash
$ ulimit -n
65536
```

#### Fluentdのインストール

```bash
$ curl -L http://toolbelt.treasuredata.com/sh/install-ubuntu-trusty-td-agent2.sh | sh
```

起動するときFluentd(td-agent)を自動に起動

```bash
$ sudo update-rc.d td-agent defaults
```

#### Slackへ連帯

* FluentdのSlackプラグインをインストールする

```bash
$ sudo /opt/td-agent/embedded/bin/fluent-gem install fluent-plugin-slack
```

* SlackのWebhookを登録

https://yourteam.slack.com/services/new/incoming-webhook

ガイドに従ってやれば良い。
これで準備完了！

## Fluentdの設定

```/etc/td-agent/td-agent.conf
## nginx
<source>
  type tail
  format none #今回はそのままのログを送信するのでnoneにする
  path /var/log/yourproject/nginx_error.log # ログファイルの場所
  tag nginx.error
  pos_file /var/log/td-agent/nginx.pos
</source>

## unicorn
<source>
  type tail
  format none 
  path /var/log/yourproject/unicorn_error.log
  tag unicorn.error
  pos_file /var/log/td-agent/error.pos
</source>

## slackに通知
<match *.error>
  type slack
  webhook_url [WEBHOOK_URL_HERE] # webhookのURL
  channel channel_notification # channel指定
  username fluentd
  flush_interval 2s # すぐに通知したいのでとりあえず2秒間隔
</match>
```

td-agentを再起動

```bash
$ sudo service td-agent restart
```

## 確認
unicornをstopして、つまりアプリケーションサーバーが動かない状態でこういうメッセージが来た！

![Screen Shot](/content/images/2017/04/c7167c49-531e-4c4f-92ea-cc2e7ecd74f2.jpg)


## 参考

* Fluentdインストール手順 
http://docs.fluentd.org/articles/install-by-deb

* FluentdのSlackプラグイン 
https://github.com/sowawa/fluent-plugin-slack

