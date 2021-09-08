+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T11:40:39Z
description = ""
draft = true
slug = "udacity-deeplearning-lecture-note-3"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - 講義ノート3"

+++


Lesson 3はConvolutional Neural Networks（畳み込みニューラルネットワーク）について勉強します。

## Intro Lesson 3

Lesson 2には一般ニューラルネットワークについて勉強いました。しかし、もしデータが画像だったり、シーケンスだったり、そのデータに対してもっといいニューラルネットワークがあります。

## Statistical Invariance

* Translation invariance：画像の中に猫があるかどうかを判定するニューラルネットワークがあります。猫の位置、左だったり、右だったり、どこでも同じ猫です！猫は位置に依存せず、どこでも同じという性質はtranslation invariance（並進不変性）といいます。
![image](https://galapagos.qiita.com/files/08282d1a-ee34-fde3-5e53-b3b30a67dd21.png)

* Weight sharing：もう一つの例：猫(kitten)についてのテキストがあります。猫というワードの意味はテキストの中に、どこでも同じでしょう。なので、猫の意味を一回学習すればよいです。つまり、猫を同じ学習重みに設定して、これはweight sharingといいます。
![image](https://galapagos.qiita.com/files/cfcb6c51-7b8a-d166-b2aa-30aca8907b67.png)

* Statistical invariants: は時間・空間に依存しないものです。実際問題にはたくさんあります。

このアイデアを基づいて、新しいニューラルネットワークを構築されました。
画像の場合、畳み込みニューラルネットワークになり、テキスト・または一般のシーケンスの場合、embeddingsとrecurrent neural networksになります。

## Convolutional Networks
さて、畳み込みニューラルネットワーク（Convolutional NetworksまたはConvnets)について学びます。

* Convnetsは空間全体にパラメータをシェアするニューラルネットワークです。
![image](https://galapagos.qiita.com/files/2da81ed6-dd1e-9b70-ea30-285d9ef44876.png)

* 入力画像は幅width、高さheight、奥行きdepth持っています。RGB画像なので、`depth=3`になります。
![image](https://galapagos.qiita.com/files/6f78226c-119e-4165-aefe-072a19c46825.png)


* 次に、その画像の一部を取って、その上にK出力の小さいニューラルネットワークを実行します。
![image](https://galapagos.qiita.com/files/23f1ff3b-b2cf-8d18-1bb2-257560e36a25.png)


* その小さいニューラルネットワークを全画像をスライドして、結果は新しい画像が得られます。その画像は違う幅・ちがう高さ・ちがう奥行きになるます。K出力のニューラルネットワークなので、奥行きKになります。 
![image](https://galapagos.qiita.com/files/37bcaf99-7e43-303f-c438-29500d5cb848.png)

以上の作業は畳み込み（convolution）と呼ばれます。畳み込み作業で、空間全体にパラメータをシェアするニューラルネットワークを実現できます。

* たくさんの畳み込みレイヤを重ねると、convolutional pyramidが得られます。
![image](https://galapagos.qiita.com/files/cf11ce68-633a-25e3-9ede-ac98ce565779.png)

（続きは明日に。。。）

