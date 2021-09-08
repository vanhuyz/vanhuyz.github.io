+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-02-13T11:15:00Z
description = ""
draft = false
slug = "how-to-apply-unique-restriction-with-soft-delete-in-rails"
tags = ["TIL", "Japanese"]
title = "Railsで論理削除を考慮するunique制約のかけ方"

+++


## 使用バージョン
* Rails 4.2.0
* Ruby 2.2.0
* MySQL 5.6
* 論理削除のgem: [paranoia](https://github.com/radar/paranoia) 2.1.0

## 目的

例えばこういう`user`モデルがあります

```ruby
# 20150119070756_create_users.rb
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name, :limit => 20
      
      t.datetime :deleted_at # <- 論理削除
      t.timestamps null: false
    end
  end
end
```

注意したいのは`deleted_at`コラムは削除した日付です。デフォルトだと、

* 非削除レコードは`deleted_at IS NULL`
* 削除済みレコードは`deleted_at IS NOT NULL`

**問題**: もし`name`を`unique制約`をかけると、削除済みレコードはそのままデータベースに残るので同じ`name`のレコードを追加することができません。<br>
**目的**: 非削除レコードの中で`name`をunique制約したい。

ネットで調べるとハマる人が少なくないようです。
今回はデータベースレベルとアプリケーションレベルの両方設定します。

## 作業内容

まず`deleted_at`はNULLだとunique複合キーを設定するのも[意味ない](http://somenotes.seesaa.net/article/132793988.html)のでNULLを避けます。

#### 設定ファイルparanoia.rbを作る 

```ruby
# config/initializers/paranoia.rb
Paranoia.default_sentinel_value = DateTime.new(0)
```

以上の１行だけで、

* 非削除レコードは`deleted_at = '0000-01-01 00:00:00'`
* 削除済みレコードは`deleted_at != '0000-01-01 00:00:00'`

になります。これで、unique複合キーを設定することができます。

#### データベースレベル：migration変更

```ruby
# 20150119070756_create_users.rb
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      # 省略
    end

    add_index :users, [:name,:deleted_at], unique: true
  end
end
```

#### アプリケーションレベル: validation追加

```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  acts_as_paranoid
  validates :name, uniqueness: { scope: :deleted_at }		
```

完了！

## 参考

* Paranoia wiki: https://github.com/radar/paranoia/wiki/Custom-sentinel-values

