+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T11:59:40Z
description = ""
draft = true
slug = "udacity-deeplearning-lecture-note-1"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - 講義ノート1"

+++


##  What is Deep Learning?

* Vincent先生はGoogleのサイエンティストで、機械学習・AIを研究しています。
* ディープラーニングは機械学習分野の一部で、データをたくさん・たくさん使って、人間しかできなかったことをコンピューターに教えることです。
* 最近、ディープラーニングがコンピュータービジョン・音声認識のほか、たくさんの分野に応用されます。例えば、新薬の発見、自然言語理解、検索結果のランキングなどです。

## Course Overview

![image](https://galapagos.qiita.com/files/4633bc9b-6a70-0cdc-a2cf-e73500f0adb3.png)


大きくは４つのレッスンがあります。

##### 1. 機械学習からディープラーニングへ
* logistic classification
* stochastic optimization
* data & parameter tuning

##### 2.  ディープニューラルネットワーク
* deep networks
* regularization

##### 3. 畳み込みニューラルネットワーク
##### 4. テキストとシーケンスのためのディープモデル
* embeddings
* recurrent models

まずはレッスン１を始めましょう！

## Solving Problems - Big and Small

昔、音声認識・コンピュータービジョンなどの各分野には全然違うテクニックを使っていました。でも今のディープラーニングはどこでも応用できます。

## Let's Get Started

* ニューラルネットワークは1980~1990に出現されました。でも、そのごろ、コンピューターは遅く、データは少なかったので何も応用できなかったです。そのため、ニューラルネットワークは長い間機械学習の世界から消えていました。
* 最近（2009から）ニューラルネットワークはまた注目されてきました。その原因は：
    * たくさんのデータがある
    * 安いと速いGPUの普及 (ゲーマーさん、ありがとう！）
* 今はディープラーニングはどこでも見られます。

## Supervised Classification

 Classification（分類）はある入力、例えば文字、にラベルを与える、例えば「B」、のタスクです。たくさんのデータ（training set・学習セット）を利用して、新しいものの分類を推測します。

## Training Your Logistic Classifier

* Logistic ClassifierはLinear Classifierとも呼びます

入力ベクトル$X$から出力ベクトル$Y$を予測問題を考えます。
例えば、ある文字認識の問題で、$X$は画像ピックセルで、$Y$は`a, b, c`という文字の中の１つです。
Logistic Classifierは$Y$と$X$の関係が線形関数で表せるのを仮定します。すなわち、

$$
WX + b = Y
$$

になります。

ここで$W$は重み行列、$b$はバイアスベクトルです。
目的は$Y$が一番正しい結果を得るために、$W$と$b$を求めることです。

これは一番単純なモデルです。

予想結果の評価にはスコアという概念があります。高いスコアほど結果が正しいようです。

以上の例で、例えばある画像はラベル$(a, b, c)$に応じてスコアが $(2.0, 1.0, 0.1)$ です。$2.0$が一番高いですからその画像は`a`でしょう。

また、確率という概念もあります。一般的に高いスコアは高い確率で、低いスコアは低い確率になります。統計学で学んだように確率の和は１です。

スコアから確率に変換するには、[softmax関数](https://en.wikipedia.org/wiki/Softmax_ function)を使います。スコアが
 $(y_ 1, y_ 2, \ldots ,y_ n)$ と確率が$ \big(s(y_ 1), s(y_ 2), \ldots ,s(y_ n)\big) $を記号すると、softmax関数は

$$
s(y_ i) = \frac{\exp(y_ i)}{\sum_ j \exp(y_ j)}
$$

になります。

[Logistic Regression](https://ja.wikipedia.org/wiki/%E3%83%AD%E3%82%B8%E3%82%B9%E3%83%86%E3%82%A3%E3%83%83%E3%82%AF%E5%9B%9E%E5%B8%B0)において、スコアは[logits](https://ja.wikipedia.org/wiki/%E3%83%AD%E3%82%B8%E3%83%83%E3%83%88)とも呼びます。

#### Quiz: Softmax関数の実装

```python
"""Softmax."""

scores = [3.0, 1.0, 0.2]

import numpy as np

def softmax(x):
    """Compute softmax values for each sets of scores in x."""
    return np.exp(x) / np.sum(np.exp(x), axis=0)
    

print(softmax(scores))

## Plot softmax curves
import matplotlib.pyplot as plt
x = np.arange(-2.0, 6.0, 0.1)
scores = np.vstack([x, np.ones_ like(x), 0.2 * np.ones_ like(x)])

plt.plot(x, softmax(scores).T, linewidth=2)
plt.show()

```

結果：

```
[ 0.8360188   0.11314284  0.05083836]
```
![image](https://galapagos.qiita.com/files/475a8b81-98d3-d044-1992-f58bac182a53.png "図1: Softmax曲線")

softmaxの実装部分は[NumPy 覚え書き](https://galapagos.qiita.com/necocen/items/4500cafae1f5f45cc937)で山本先生が説明してくれるはずなのでパスします。

プロットの実装部分はNumPy・matplotlibまだ慣れていないのでちょっとややこしいかもしれません。アイデアとしては

* `x`は$[-2.0, -1.9, ..., 6.0]$
* `np.ones_ like(x)`は$[1.0, 1.0, ..., 1.0]$
* `0.2 * np.ones_ like(x)`は$[0.2, 0.2, ..., 0.2]$
* `scores`は

$$
\left[
 \begin{array}{ccc}
-2.0, & -1.9, &\ldots, &6.0 \\
1.0, &1.0, &\ldots, &1.0 \\
0.2, &0.2,&\ldots, &0.2 \\
\end{array}
\right]
$$

この`scores`を`softmax`計算してプロットします。図の中にはは青線は**x**、緑線は**1.0**、赤線は**0.2**です。**x**のはじめはスコアが一番小さいから確率は0に近いですが、どんどん大きくなって、最終は1に近づきますね。他の線はスコアが変わりませんが、**x**のスコアが変わったせいで確率がどんどん小さくなります。どの時点にしても確率の和は1という性質も確認できますね。

#### Quiz: softmaxの大事な性質

* もし`scores`の全部に10倍をかけると、確率は1か0に近づく
* もし`scores`の全部に10倍を割ると、確率は一様分布に近づく

## One-Hot Encoding

ラベルをベクトル化します。1つの要素を1にし、残りは0にします。
例えば$(a,b,c)$の3つのラベルがあります。

* aのベクトルは$[1,0,0]$
* bのベクトルは$[0,1,0]$
* cのベクトルは$[0,0,1]$

このベクトル化作業（ベクトルにマッピングする）はOne-Hot Encodingといいます。


### Cross Entropy

確率ベクトル$S$とラベルベクトル$L$の距離です。以下の公式で計算されます。

$$
D(S,L)=-\sum_ i L_ i\log(S_ i)
$$

ここで$D$は距離です。$D$が小さいほど、$S$と$L$が近く（値が同じに）なります。

## 今までのまとめ
![image](https://galapagos.qiita.com/files/77cc9076-bbca-18c3-6104-30d879fbcd72.png)


### Minimizing Cross Entropy
ロス関数(loss function)[^1]：は学習セットのcross entropyの平均。すなわち、

$$
loss = \frac{1}{N}\sum_ i D\big(S(WX_ i+b),L_ i\big)
$$

となります。
ここで、$N$はデータ数、$i$は$i$番目のデータです。

学習データ$X$とラベル$L$はもう決まっているので、lossの値は$W$と$b$に依存します。lossは小さければ小さいほど、予想結果とラベルが近くなるので、結局の問題はlossを最小化するように$W$と$b$を求めます。

これを解決するのは一番簡単な手法は[gradient descent(最急降下法)](https://ja.wikipedia.org/wiki/%E6%9C%80%E6%80%A5%E9%99%8D%E4%B8%8B%E6%B3%95)です。この手法を理解するために、Courseraの機械学習コースWeek1を見ましょう！[^2]


[^1]: cost functionとも言います
[^2]: https://class.coursera.org/ml-005/lecture

