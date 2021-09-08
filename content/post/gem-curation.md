+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-01-30T04:50:00Z
description = ""
draft = false
slug = "gem-curation"
tags = ["TIL", "Japanese"]
title = "API開発用必要なGemのまとめ（2015/08更新）"

+++


初めてRailsプロジェクトに入ったとき、Gemfileを見ても全くわからなかった。Rails初心者のため必要なGemをまとめました。良いGemを発見したらどんどん更新していこうと思います。

2015/08/13時点

## データベース関連

#### [mysql2](https://github.com/brianmario/mysql2)

* MySQLアダプタ
* MySQLを使うなら必須

#### [redis](https://github.com/redis/redis-rb)

* Ruby client library for Redis
* cache, session保存用

#### [ar-octopus](https://github.com/tchandy/octopus)

* レプリケーション (master+slave管理)

#### [switch_point](https://github.com/eagletmt/switch_point) (※ 2015/08/13追加）

* ar-octopusの代替。機能はar-octopusまで高くないが、master+slave切り替えぐらいなら十分

#### [paranoia](https://github.com/radar/paranoia)

* 論理削除

## API

#### [grape](https://github.com/intridea/grape)

* REST API生成

#### [grape-entity](https://github.com/intridea/grape-entity)

* grapeのサーポート
* レスポンス(json)を簡単に記述できる

#### [grape-swagger](https://github.com/tim-vandecasteele/grape-swagger)

* grapeのサーポート
* Swaggerドキュメントを生成する

#### [kramdown](https://github.com/gettalong/kramdown)

* grape-swaggerのサーポート
* markdownパーサー

#### [rack-cors](https://github.com/cyu/rack-cors)

* Cross-Origin Resource Sharing (CORS)をハンドリング
* Swaggerを別のサーバーに置く場合はこれが必須

#### [kaminari](https://github.com/amatsuda/kaminari)または[grape-kaminari](https://github.com/monterail/grape-kaminari)

* APIのページネーション

## テスト
#### [rspec-rails](https://github.com/rspec/rspec-rails)

* テストフレームワーク

#### [capybara](https://github.com/jnicklas/capybara) (※ 2015/08/13追加）

* Websiteのテストフレームワーク

#### [shoulda-matchers](https://github.com/thoughtbot/shoulda-matchers) (※ 2015/08/13追加）

* モデルテストに便利

#### [spork-rails](https://github.com/sporkrb/spork-rails)

* テストの実行時間を短縮してくれる

#### [factory_girl_rails](https://github.com/thoughtbot/factory_girl_rails)

* テストデータを用意する

#### [database_cleaner](https://github.com/DatabaseCleaner/database_cleaner)

* FactoryGirlにて作成するテストデータを、テストを実行する度に消去する

#### [turnip](https://github.com/jnicklas/turnip) (※ 2015/08/13追加）

* Rspecの中にCucumber風コードを書ける

#### [capybara-screenshot](https://github.com/mattheworiordan/capybara-screenshot) (※ 2015/08/13追加）

* Capybaraのテストが失敗したら自動的にスクリーンショートを撮る

## ユーザ認証

#### [devise](https://github.com/plataformatec/devise)

* ユーザ登録・ログインまで全てやってくれる

## 管理画面

#### [activeadmin](https://github.com/activeadmin/activeadmin)

* 10分で作れる管理画面
 
## Push通知

#### [houston](https://github.com/nomad/Houston)

* iOSデバイスにpush通知を送る定番のgem
* invalid tokenが混じると通知が届かないバグがある（2014/12)

#### [parse-ruby-client](https://github.com/adelevie/parse-ruby-client) (※ 2015/08/13追加）

* Parseと連携

## バックグラウンド処理

#### [sidekiq](https://github.com/mperham/sidekiq)

* バックグラウンドworkerの定番

#### [sucker_punch](https://github.com/brandonhilkert/sucker_punch) (※ 2015/08/13追加）

* Sidekiqはredisが必須なのでインフラコストが上がる。メールをバックグラウンドで送るぐらいならsucker_punchの方が軽い

#### [whenever](https://github.com/javan/whenever)

* Cron jobs管理

## 画像関連

#### [carrierwave](https://github.com/carrierwaveuploader/carrierwave)

* 画像アップロードの定番

#### [paperclip](https://github.com/thoughtbot/paperclip) (※ 2015/08/13追加）

* これも画像アップロードの定番

#### [fog](https://github.com/fog/fog)

* carrierwaveのサーポート
* S3にアップロード

#### [rmagick](https://rubygems.org/gems/rmagick)

* 画像処理（サイズ調整、角丸など）に便利

## デプロイ用

#### [unicorn](https://rubygems.org/gems/unicorn)

* 本番用のWebサーバー
* 設定は面倒

#### [capistrano](https://github.com/capistrano/capistrano)

* 有名なデプロイツール

#### [capistrano-rails](https://github.com/capistrano/rails)

* db migrateとassets compileタスクを用意する

#### [capistrano-rbenv](https://github.com/capistrano/rbenv)

* デプロイ先にrbenv使ったらこれが必要

#### [capistrano3-unicorn](https://github.com/tablexi/capistrano3-unicorn)

* デプロイした後unicornを再起動してくれる
* Ubuntu14.04とCentOS7は動作確認済み

## 開発サポート

#### [pry-rails](https://github.com/rweng/pry-rails) (※ 2015/08/13追加）

* Rails consoleのirbの代わりにpryを使う

#### [rails-erd](https://github.com/voormedia/rails-erd) (※ 2015/08/13追加） 

* テーブル間の関係の図を生成する

#### [bullet](https://github.com/flyerhzm/bullet) (※ 2015/08/13追加）

* N+1問題チェック

#### [better_errors](https://github.com/charliesome/better_errors) (※ 2015/08/13追加）

* デフォルトより優れているエラーページ

#### [settingslogic](https://github.com/binarylogic/settingslogic) (※ 2015/08/13追加）

* 定数をymlとして管理する

#### [config](https://github.com/railsconfig/config) (※ 2015/08/13追加）

* settingslogicの代替。定数を環境ごとに別のファイルで管理できる

#### [brakeman](https://github.com/presidentbeef/brakeman) (※ 2015/08/13追加）

* 脆弱性診断

#### [simplecov](https://github.com/colszowka/simplecov) (※ 2015/08/13追加）

* テストカバレッジ生成

#### [simplecov-rcov](https://github.com/fguillen/simplecov-rcov) (※ 2015/08/13追加）

* simplecovからrcovスタイルのindex.htmlファイルを生成

