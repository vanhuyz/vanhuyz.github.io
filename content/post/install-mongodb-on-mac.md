+++
author = "Huy Van"
categories = ["MongoDB", "Japanese"]
date = 2016-08-31T06:10:00Z
description = ""
draft = false
slug = "install-mongodb-on-mac"
tags = ["MongoDB", "Japanese"]
title = "MacでMongoDBメモ"

+++


機械学習で大量のデータを扱いにはデータベースが欲しくなります。SQLデータベースは設計が面倒なので今回はNoSQLのMongoDBを選択しました。

## 設定編

### インストール

```bash
$ brew update
$ brew install mongodb
```

### データベースの保存先のディレクトリを作る

```bash
$ sudo mkdir -p /data/db
$ sudo chown -R 777 /data
```

`/data/db`はデフォルトなのでおすすめです。
777は多分危ないかもしれませんので、正しい権限を教えてください :bow: 

## 起動
### サーバー

```bash
$ mongod
```

### クライエント

```bash
$ mongo
MongoDB shell version: 3.2.9
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
  http://docs.mongodb.org/
Questions? Try the support group
  http://groups.google.com/group/mongodb-user

> use mydb
switched to db mydb
> a = { content: "ほげほげ"  }
{ "content" : "ほげほげ" }
> db.articles.save(a)
WriteResult({ "nInserted" : 1 })
> db.articles.find()
{ "_id" : ObjectId("57c66c9142b4bc1b24ea7fc7"), "content" : "ほげほげ" }
```

### バックアップ

```bash
$ mongodump --out db/dump --db dbname
```

### リストア

```bash
$ mongorestore db/dump --db maybe_different_dbname
```

## PythonでMongoDBを使う
いつかのORMが存在しますが[mongoengine](https://github.com/MongoEngine/mongoengine)が一番使いやすいのでmongoengineを使ってみます。

```bash
$ pip install mongoengine
```

モデルの定義

```python:article.py
from mongoengine import * 

connect('dbname')

class Article(Document):
  content = StringField(required=True)
```

レコード追加

```python
from article import Article

article = Article(content = 'ほげほげ')
article.save()
```

全レコードを１個ずつ読み込み

```python
from article import Article

for article in Article.objects
  print(article.content)
```

