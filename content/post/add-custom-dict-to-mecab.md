+++
author = "Huy Van"
categories = ["TIL", "Japanese"]
date = 2016-06-08T07:25:00Z
description = ""
draft = false
slug = "add-custom-dict-to-mecab"
tags = ["TIL", "Japanese"]
title = "MeCabに人名辞書を追加"

+++


生成された辞書（バイナリ）はどこでも使えますので、辞書生成はMac側で行います。

## インストール

```bash
$ brew install mecab mecab-ipadic
```

## 構成

```bash
## /usr/local/Cellar/mecab
└── 0.996
    ├── AUTHORS
    ├── COPYING
    ├── ChangeLog
    ├── INSTALL_RECEIPT.json
    ├── NEWS
    ├── README
    ├── bin
    │   ├── mecab
    │   └── mecab-config
    ├── include
    │   └── mecab.h
    ├── lib
    │   ├── libmecab.2.dylib
    │   ├── libmecab.a
    │   └── libmecab.dylib -> libmecab.2.dylib
    ├── libexec
    │   └── mecab
    │       ├── mecab-cost-train
    │       ├── mecab-dict-gen
    │       ├── mecab-dict-index
    │       ├── mecab-system-eval
    │       └── mecab-test-gen
    └── share
        └── man
            └── man1
                └── mecab.1
```

```bash
## /usr/local/Cellar/mecab-ipadic
└── 2.7.0-20070801
    ├── AUTHORS
    ├── COPYING
    ├── ChangeLog
    ├── INSTALL_RECEIPT.json
    ├── NEWS
    ├── README
    └── lib
        └── mecab
            └── dic
                └── ipadic
                    ├── char.bin
                    ├── dicrc
                    ├── left-id.def
                    ├── matrix.bin
                    ├── pos-id.def
                    ├── rewrite.def
                    ├── right-id.def
                    ├── sys.dic
                    └── unk.dic
```

## 辞書をダウンロード

[辞書リスト](http://www.mwsoft.jp/programming/munou/ime_dictionary_link.html#person)の中からこの[人名辞書](http://www.vector.co.jp/download/file/data/writing/fh035171.html)と[女の子の名前](http://www.vector.co.jp/soft/data/writing/se021655.html)が良さそうなのでダウンロードします。

## MeCab用のcsv生成

ダウンロードしたファイルはテキストなのでMeCab用のcsvフォーマットに変換することが必要です。

例えば、

```
亜衣,,,,名詞,固有名詞,人名,姓,*,*,あい,アイ,jinmei
```

2から4番目までは左文脈ID、右文脈ID、コストの順番になります。これらの値はMeCabで推定しますので、空のままでよいです。
最後はなんでもいいです。今は人名辞書なので`jinmei`にしました。

Rubyなどのスクリプト言語で適当に変換スクリプトを書いたらOKです。注意するのはもとのファイルはJIS-SHIFT文字コードが多いのでUTF-8に変換が必要です。

## コスト推定
コストは小さいほど出現率が高いそうです。適当に小さい値を入れてもいいですが、既存の辞書と相性悪くなるのでちゃんと既存辞書からコスト推定を行います。

まず、[学習済みモデル](https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7bnc5aFZSTE9qNnM)をダウンロードします。
モデルファイル文字コードもEUC_JPのためUTF-8に変換します。
nkfが入っていない方は、`$ brew install nkf`してください。

```bash
$ nkf --overwrite -Ew mecab-ipadic-2.7.0-20070801.model
```

モデルファイルのcharsetをutf-8に変更：

```diff:mecab-ipadic-2.7.0-20070801.model
-charset: euc-jp
+charset: utf-8
```

brewからインストールしたipadicは`feature.def`ファイルがないので、途中でエラーがでました。[ipadic](https://sourceforge.net/projects/mecab/files/mecab-ipadic/)をダウンロードして、`feature.def`をシステムのipadicディレクトリにコピーしたら良いです。

```bash
$ cp feature.def /usr/local/lib/mecab/dic/ipadic
```

コスト推定の実行：

```bash
$ /usr/local/Cellar/mecab/0.996/libexec/mecab/mecab-dict-index -m resources/mecab-ipadic-2.7.0-20070801.model -d /usr/local/lib/mecab/dic/ipadic -u output/jinmei.csv -f utf-8 -t utf-8 -a output/jinmei_nocost.csv
```

> -m: モデルファイル
-d: ipadic辞書
-a: 入力csv
-u: 出力csv（コストが含まれる）
-f: 入力csvのcharset（デフォルトEUC-JP）
-t: 出力csvのcharset（デフォルトEUC-JP）

結果：

> resources/mecab-ipadic-2.7.0-20070801.model is not a binary model. reopen it as text mode...
reading output/jinmei_nocost.csv ...
done!

以上のメッセージが出たら成功です。出力csvを確認します：

```
亜衣,1290,1290,7569,名詞,固有名詞,人名,姓,*,*,あい,アイ,jinmei
```

## csvからdic（バイナリ）に変換

同じくMeCabのmecab-dict-indexコマンドを使います。

```bash
$ /usr/local/Cellar/mecab/0.996/libexec/mecab/mecab-dict-index -d /usr/local/lib/mecab/dic/ipadic -u output/jinmei.dic -f utf-8 -t utf-8 output/jinmei.csv
```

> reading output/jinmei.csv ... 11892
emitting double-array: 100% |###########################################|
done!

`jinmei.dic`が生成されたら成功です。

## ユーザー辞書をMeCabに追加

mecabrcファイルに以下の行を追加してください。

```
userdic = /path/to/jinmei.dic
```

mecabrcのパスはOSによって違います。

Macの場合: `/usr/local/etc/mecabrc`
Ubuntuの場合: `/etc/mecabrc`

## お試し

```bash
$ mecab
相内信弥
相内  名詞,固有名詞,人名,姓,*,*,あいうち,アイウチ,jinmei
信弥  名詞,固有名詞,人名,名,*,*,しんや,シンヤ,jinmei
```


## 参考
* [公式ドキュメント](http://taku910.github.io/mecab/dic.html)
* [Mecabに人名辞書を追加](http://qiita.com/awakia/items/9a0dec41d91c997c74b4)

