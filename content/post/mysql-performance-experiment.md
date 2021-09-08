+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2015-02-03T09:57:00Z
description = ""
draft = false
slug = "mysql-performance-experiment"
tags = ["TIL", "Japanese"]
title = "MySQLで大量のデータを挿入して、SELECTパーフォマンスを比較する"

+++


大量のデータを扱う機会があるため、データが非常に多いときMySQLのパフォーマンスはどうなるかを検討した。

* 検討するテーブル：

![Screen Shot 2015-02-03 at 3.04.37 PM.png](/content/images/2017/04/a4cf4ef4-6350-c66c-c247-abe26598e483.jpg)
 

* 検討したいquery：

```mysql
 SELECT * FROM activity_records WHERE date BETWEEN '2014-01-01' AND '2014-04-10';
```

なので`date`コラムにindexをつけた。このテーブルに**5億ぐらいのレコード**があるとき、レスポンス時間はどのぐらいなのか検討したい。

* 環境：

![Screen Shot 2015-02-03 at 4.03.32 PM 1.png](https://qiita-image-store.s3.amazonaws.com/1758/60017/d9aabe68-44a1-62e6-ab5c-7c54b17c54ae.png)
SSD: 256GB


## テーブルに5億レコードを挿入する

この記事は大変参考になりました：[Mass inserting data in Rails without killing your performance](https://www.coffeepowered.net/2009/01/23/mass-inserting-data-in-rails-without-killing-your-performance/)

単純の`(5*10**8).times { Model.create(options) }`なら**20日間**がかかるのに対し、１つのINSERTを大量のデータをまとめ（[bulk insert](http://dev.mysql.com/doc/refman/5.6/en/optimizing-innodb-bulk-data-loading.html)と呼ばれる）すると**１０時間**しかかからない。

最終のrakeタスクのコードは以下となる

```ruby
# insert_data.rake
namespace :insert_data do
  desc "Insert dummy activity records"

  task run: :environment do
    TOTAL_RECORDS = 500000000
    RECORDS_PER_TIME = 50000

    CONN = ActiveRecord::Base.connection
    start_time = Time.now
    (TOTAL_RECORDS/RECORDS_PER_TIME).times do |i|
      inserts = Array.new
      RECORDS_PER_TIME.times do |j|
        date = (Time.new(2014, 1, 1, 00, 00, 00) + i * RECORDS_PER_TIME * 0.01 + j * 0.01).to_s(:db)
        created_at = Time.now.to_s(:db)
        updated_at = Time.now.to_s(:db)
        inserts.push "(1, #{rand(1..3)}, '#{date}', '#{created_at}', '#{updated_at}')"  
      end
      sql = "INSERT INTO activity_records (`user_id`, `hogehoge_id`, `date`,`created_at`,`updated_at`) VALUES #{inserts.join(", ")}"  
      CONN.execute sql
      end_time = Time.now
      puts "Inserted #{RECORDS_PER_TIME*(i+1)} records in #{(end_time - start_time)} seconds"
    end
  end
end
```

途中でディスクフルになったが、ほぼ5億レコードになった。

```mysql
mysql> SELECT COUNT(*) FROM activity_records;
+-----------+
| COUNT(*)  |
+-----------+
| 480101568 |
+-----------+
1 row in set (2 min 8.37 sec)
```


## パーフォマンスを比較

```mysql
mysql> SELECT * FROM activity_records WHERE date = '2014-02-15';

(結果は省略）

100 rows in set (0.00 sec)

mysql> SELECT * FROM activity_records WHERE date BETWEEN '2014-02-15' AND '2014-02-16' limit 10;

(結果は省略）

10 rows in set (0.00 sec)

mysql> SELECT * FROM activity_records WHERE date BETWEEN '2014-02-15' AND '2014-02-16';

(結果は省略）

5550000 rows in set (25.96 sec)

```

抽出結果が少ない場合はすぐ結果が出ますが、抽出結果が多い（555万）の場合は遅い(25.96秒）。このqueryをプロフィリングすると

```mysql
mysql> show profile for query 23;
+----------------------+-----------+
| Status               | Duration  |
+----------------------+-----------+
| starting             |  0.000095 |
| checking permissions |  0.000012 |
| Opening tables       |  0.000020 |
| init                 |  0.000031 |
| System lock          |  0.000008 |
| optimizing           |  0.000011 |
| statistics           |  0.000105 |
| preparing            |  0.000014 |
| executing            |  0.000003 |
| Sending data         | 25.960623 |
| end                  |  0.000012 |
| query end            |  0.000006 |
| closing tables       |  0.000032 |
| freeing items        |  0.000053 |
| logging slow query   |  0.000008 |
| cleaning up          |  0.000020 |
+----------------------+-----------+
16 rows in set, 1 warning (0.01 sec)
```

何も言えないです。やはり大量データを抽出するというqueryを避けた方が良い。今考えられるのはbatch処理と別の統計テーブルを用意することです。

## おまけ

5億レコードの容量はどのぐらいなのか調べてみると、

```mysql
mysql> show table status like 'activity_records'\G
*************************** 1. row ***************************
           Name: activity_records
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 446974130
 Avg_row_length: 49
    Data_length: 21990735872
Max_data_length: 0
   Index_length: 22140682240
      Data_free: 5242880
 Auto_increment: 480151569
    Create_time: 2015-01-30 15:55:56
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.00 sec)
```

データサイズとインデックスサイズの合計で**42GB**ぐらいです！
だから、MacのSSDがフルになっているね。検討が終わったら全部消します！

