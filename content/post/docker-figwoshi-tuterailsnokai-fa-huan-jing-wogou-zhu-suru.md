+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-01-19T12:38:00Z
description = ""
draft = false
slug = "docker-figwoshi-tuterailsnokai-fa-huan-jing-wogou-zhu-suru"
tags = ["TIL", "Japanese"]
title = "Docker + Figを使ってRailsの開発環境を構築する"

+++


[前回](https://vanhuyz.com/try-magical-docker/)はDockerで構築したアプリを使うのを述べました。今回は実際どうやってそのRailsアプリを構築するのかを紹介します。

## 構築したい環境
* Ruby 2.2
* Rails 4.2.0
* MySQL
* Redis

## シナリオ
以上の条件でコンテナを３つ準備します。

* web  ： Railsアプリ。もちろんRubyが入ってるので今回は[official ruby image](https://registry.hub.docker.com/u/library/ruby/)をベースにコンテナを作ります
* db   ： [official MySQL image](https://registry.hub.docker.com/_/mysql/)を採用します
* redis： [official Redis image](https://registry.hub.docker.com/_/redis/)を採用します
 
気づいたのは使った３つのofficial imagesは全部Debian (Ubuntuと似ているLinux OS）です。違うOSでも問題ないでしょう。

webコンテナの`Dockerfile`

```
# Docker hubのruby imageをpullする。2.2.0を指定
FROM ruby:2.2.0                
MAINTAINER vanhuyz

# 必要なパッケージをインストール
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev 

# myappディレクトリーをwebコンテナに作成
RUN mkdir /myapp

# RUN, CMDのworking directoryを指定 
WORKDIR /myapp 

 # hostマシンのGemfileをwebコンテナにコピーする
ADD Gemfile /myapp/Gemfile

# webコンテナにbundle installを実行
RUN bundle install 

# 生成されたファイルをwebコンテナにコピーする
ADD . /myapp 
```

dbとredisはofficial imageのままを使うから`Dockerfile`は必要ない。

************************

さて、３つのコンテナはどうやって連結するのか。Dockerの標準機能では`run`するとき[`--link`](https://docs.docker.com/userguide/dockerlinks/)を使うとよいらしいです。ただし、コマンドは長くて本当に面倒！毎回アプリを起動するたびにこういう長いコマンドを打つのが疲れますよね。

では本当に便利なツールを紹介していきます :wink: :blush: 

## Figの登場
[Fig](http://www.fig.sh/index.html)は複数コンテナを管理するツールです。`$ fig up`で一気に全部のコンテナを起動するのが魅力！

Figの設定は全て`fig.yml`に書いています。今回の`fig.yml`は以下のようになります。

```yaml
# fig.yml

# dbコンテナの設定
db:
  # Docker hubのmysql imageからpullする（FROM mysqlと相当）
  image: mysql

　# 環境変数を指定
  environment:
    MYSQL_DATABASE: myapp_development
    MYSQL_ROOT_PASSWORD: root
  
  # コンテナの0.0.0.0:3306にホストの3306ポートにフォワーディング
  ports:
     - "0.0.0.0:3306:3306"

# redisコンテナ
redis:
  image: redis
  ports:
    - "0.0.0.0:6379:6379"

# webコンテナ
web:

  # (imageではなく)buildというのはDockerfileの場所を指定する。コンテナ設定はimage/buildのいずれか必要
  build: .

  # webコンテナにこのコマンドを実行する
  command: bundle exec rails s -b 0.0.0.0

  # volumn指定
  volumes:
    - .:/myapp
  
  # ポートフォワーディング
  ports:
    - "3000:3000"
  
  # dbとredisを紐付く
  links:
    - db
    - redis
```

Rails 4.2.0を設定する`Gemflie` を準備します。

```ruby
# Gemfile
source 'https://rubygems.org'
gem 'rails', '4.2.0'
```

## 実行
現在のファイル構成：

```
$ tree
.
├── Dockerfile
├── Gemfile
└── fig.yml
```

準備ができましたので次のコマンドを実行します。

```sh
$ fig run web rails new . --force --database=mysql --skip-bundle
```

これはwebコンテナの中に新しいrailsアプリを作ります。`fig.yml`にそって実行します。まずdb、webコンテナを作って、次にwebコンテナをDockerfileからbuildします。実行が終わったら、Railsの初期コードが生成されます。

```
$ ls
Dockerfile    Gemfile.lock  Rakefile      bin/          config.ru     fig.yml       log/          test/         vendor/
Gemfile       README.rdoc   app/          config/       db/           lib/          public/       tmp/
```

これで新しい`Gemfile`が生成されました。rubyコードの中javascriptの実行が必要らしいので以下の行をコメントアウトします。

```rb
gem 'therubyracer', platforms: :ruby
```

次に`config/database.yml`にデータベースを設定します。

```rb
default: &default
  adapter: mysql2
  encoding: utf8
  database: mysql
  pool: 5
  username: root
  password: root
  host: db          # hostをdbコンテナを指定

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test
```

設定できたらコンテナに反映します。

```bash
$ fig build
```

注意したいのは`fig.xml`または`Gemfile`に変更があるとき`$fig build`を実行しましょう。

データベースを作ります。

```bash
$ fig run web rake db:create
```

全部が揃いましたので、最後にサーバーを起動します。

```bash
$ fig up
```

このコマンドで一気に３つのコンテナを起動できます。結果確認は[前回](https://vanhuyz.com/try-magical-docker/)の記事を参考してください。

## 苦労したとこと

* `fig.yml`の中に`ports`を指定しないとデフォルトでローカルホストになっているので`boot2docker` マシンの外からアクセスできない。Macからアクセスしたい場合は`0.0.0.0`を指定する必要です。

## 終わりに

* 今回はいろんなofficial imagesを活用しました。official imagesといっても完璧ではないので検証する必要があります。
* Phusion社の[baseimage](https://registry.hub.docker.com/u/phusion/baseimage/) を利用するのがいいかもしれない。Ubuntuのいろんな不具合を修正されたから。
* 今回はここまでです。次に`nginx`と`unicorn`、そして`rails migration`も設定したいです。

