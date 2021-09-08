+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T11:24:55Z
description = ""
draft = true
slug = "udacity-deeplearning-lecture-note-4"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - 講義ノート4"

+++


Convolutional Neural Networks（畳み込みニューラルネットワーク）の続きです。前半は[こちら](https://vanhuyz.com/udacity-deeplearning-lecture-note-3/)。

## Convolutional Networks
### 主な概念
* **ストライド (stride):** フィルタを順に適用していく際に、何ピクセルずらすのかという値です。
    * stride = 1: 出力が入力の**大体**同じサイズになります。
    * stride = 2: 出力が入力の**大体**半分のサイズになります。
![image](https://galapagos.qiita.com/files/62f9a7c5-e367-0fa9-d6d7-046035add44c.png)

* **大体**といった理由は画像の端に何をするかによって変わるためです。
    * **valid padding:** 普通に画像の端からフィルタを適用していきます。こうすると、例えばstride = 1でも出力が入力より少し小さくなります。
    * **same padding:** 例えばstride = 1の場合、出力がちょうど入力と同じサイズになるために、元の画像の周りに0を詰め込んでいきます。


### 畳みこみ (Convolution) についての補足

離散畳み込み[^1] の計算は以下のイメージです。

![Screen Shot 2016-06-20 at 01.42.57.png](https://galapagos.qiita.com/files/065b7af3-4bbd-e6b8-938d-fa1261230063.png)

例:

![image_sample](/content/images/2017/04/Screen-Shot-2017-04-29-at-8.32.21-PM.png)

と

![filter_sample](/content/images/2017/04/Screen-Shot-2017-04-29-at-8.32.31-PM.png)

の畳み込み(stride=1, valid padding)を計算します。

まずは赤の部分:

$$
(3\times0 + 3\times1 + 2\times2 + 0\times2) + (0\times2 + 1\times0 + 0\times1) + (3\times0+1\times1+2\times2) = 12
$$

同様に全部適用すると、以下の結果が得られます:

![conv_result](/content/images/2017/04/Screen-Shot-2017-04-29-at-8.36.08-PM.png)

## Pooling

* 畳み込み層でstride=1を使って、たくさんフィルタを使うとパラメータ数がどんどん多くなります。過学習と計算速度と考えるとパラメータ数を減らすことが必要です。
* もしstride=2を使うと、たくさんの情報がなくなってしまいます。そのため、stride=1にして、得られた結果を近所の要素をなんとか結合するとより良い結果がでます。この工夫はプーリングです。
* 一番使われるのはマックスプーリング (max pooling) です。

![image](https://galapagos.qiita.com/files/6540d2ae-a55c-e83c-d7df-9354f472710d.png)

* よく使われるパータンは: 畳み込み → ReLU → プーリング
* プーリングはtranslation invarianceの少し効果があります。

## その他のGoogleテクニック
というのは[GoogLeNet](http://arxiv.org/pdf/1409.4842v1.pdf)で使うテクニックです。Googleだけ主唱で、どうやら良いらしいです。

### 1x1 Convolutions

![image](https://galapagos.qiita.com/files/5582e5de-5986-c74f-b3ed-915fd6f3e015.png)

実際には1x1 convolutionsを使うことがあります。これはパラメータを増やすため一番簡単で計算楽な方法です(畳み込みではなくただの行列掛け算です）。

### Inception Module

![image](https://galapagos.qiita.com/files/63496f46-bc76-b67f-2914-7b69fbf69343.png)

1つの層でたくさんの畳み込み、プーリングなどを組み合わせて、結果をでかいベクトルに順序に並ぶことです。

[^1]: 機械学習分野ではこれを畳み込みと言いますが、数学の厳密って言えば畳込みではなく、[相互相関](https://ja.wikipedia.org/wiki/%E7%9B%B8%E4%BA%92%E7%9B%B8%E9%96%A2%E9%96%A2%E6%95%B0) (cross-correlation) です。

