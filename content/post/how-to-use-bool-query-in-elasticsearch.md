+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2016-04-25T03:25:00Z
description = ""
draft = false
slug = "how-to-use-bool-query-in-elasticsearch"
tags = ["TIL", "Japanese"]
title = "Elasticsearchのbool queryを利用してAND OR NOTを書いてみる"

+++


※ この記事を[Qiita](http://qiita.com/vanhuyz/items/04a6871ae5f53ba5a97f)にも載せました！

初めてElasticsearchのクエリをビルドしたのでいろいろハマりました。SQLの世界観とちょっと違っていて、なれるまで時間がかかると思います。でも、なれたら複雑な検索条件をSQLより簡単に書けるかも知れません。

では、Elasticsearchの基礎クエリの一つ、`Bool Query`を解説してみます。

## Bool Queryについて

Elasticsearch 2.0から[andクエリ](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/query-dsl-and-query.html)と[orクエリ](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/query-dsl-or-query.html)は全部非推奨になり、その代わりに[boolクエリ](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/query-dsl-bool-query.html)の方が推奨されます。Boolクエリは複数のクエリを組み合わせる（つまりAND、OR、NOTで結合）のに使います。

Boolクエリは４種類があります：must、 filter、 should、 must_ notです。

<table>
<tr>
<th>クエリ</th>
<th>説明</th>
</tr>
<tr>
<td>must</td>
<td>ANDとして使います。小クエリは総合スコアに影響を与えます</td>
</tr>
<tr>
<td>filter</td>
<td>mustと同じくANDです。ただし、スコアは無視されます（総合スコアに影響を与えません</td>
</tr>
<tr>
<td>should</td>
<td>ORとして使えます。 minimum_should_match パラメータで最低マッチする条項の数が指定できます[^n]。</td>
</tr>
<tr>
<td>must_ not</td>
<td>NOTです。</td>
</tr>
</table>

## 使用例

### AND条件

例えば、SQLはこういうSELECT文があります。

```postgres
SELECT * FROM users WHERE age >= 25 AND salary >= 500000;
```

Elasticsearchのクエリに書き直すと、

```json
{
  "query": {
    "bool" : {
      "must" : [
        { "range" : { "age" : { "gte": 25 } } },
        { "range" : { "salary" : { "gte": 500000  } } }
      ]
    }
  }
}
```

になります。今回は`must`を使いますが、スコア無視したい場合は`filter`を使えばいいのです。


### OR条件

```postgres
SELECT * FROM users WHERE age >= 25 OR salary >= 500000;
```

Elasticsearchのクエリに書き直すと、

```json
{
  "query": {
    "bool" : {
      "should" : [
        { "range" : { "age" : { "gte": 25 } } },
        { "range" : { "salary" : { "gte": 500000  } } }
      ],
      "minimum_ should_ match" : 1
    }
  }
}
```

になります。

### NOT条件

```postgres
SELECT * FROM users WHERE user_ id NOT BETWEEN 525 AND 600;
```

Elasticsearchのクエリに書き直すと、

```json
{
  "query": {
    "bool" : {
      "must_ not" : {
        "range" : { "user_ id" : { "gte": 525, "lte": 600  } }
      }
    }
  }
}
```

になります。

### AND、OR、NOTを組み合わせてみる

```postgres
SELECT * FROM users WHERE (salary >= 500000 AND status = 'active')
                      OR (user_ id NOT BETWEEN 525 AND 600);
```

Elasticsearchのクエリに書き直すと、

```json
{
  "query": {
    "bool" : {
      "should" : [
         { 
           "bool" : {
             "must": [
               { "range": { "salary" : { "gte": 500000 } } },
               { "term": {"status": "active" } }
             ]
           } 
         },
         {
           "bool": {
             "must_ not" : {
               "range" : { "user_ id" : { "gte": 525, "lte": 600 } } 
             }
           }
         } 
       ]
    }
  }
}
```

になります。

注意したいのはboolクエリはネストができるため、いくら複雑な条件にしても表現できます。


## まとめ

<table>
<tr>
<th>条件</th>
<th>SQL</th>
<th>Elasticsearch</th>
</tr>
<tr>
<td> AND </td>
<td> WHERE A AND B </td>
<td> {"bool": {"must": [A,B]} </td>
</tr>
<tr>
<td> OR </td>
<td>WHERE A OR B </td>
<td> {"bool": {"should": [A,B]} </td>
</tr>
<tr>
<td> NOT</td>
<td>WHERE NOT A</td>
<td>{"bool": {"must_ not": A} </td>
</tr>

</table>

## おまけ
Elasticsearchクエリは[ポーランド記法](https://ja.wikipedia.org/wiki/%E3%83%9D%E3%83%BC%E3%83%A9%E3%83%B3%E3%83%89%E8%A8%98%E6%B3%95)と似ていますね。
例えば、`3+4`だったら`+ 3 4`になり、`(1 + 5) * (2 + 3)`だったら`(* (+ 1 5) (+ 2 3))`になりますね。なかなか面白いです！

では、よいサーチライフを！

## 参考文献

* [Bool Query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html)
* [Combining Queries](https://www.elastic.co/guide/en/elasticsearch/guide/current/bool-query.html)

[^n]: 公式ドキュメントには書いてないがおそらくデフォルト値は１です。

