+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-04-18T04:57:00Z
description = ""
draft = false
slug = "coursera-machinelearning-assignment-8"
tags = ["Machine Learning", "Japanese"]
title = "Coursera 機械学習 - プログラミング課題8解答例"

+++


ソースコード： https://github.com/vanhuyz/coursera-ml/tree/master/machine-learning-ex8/ex8

## 概要

今回の課題は２パートに分かれています。前半は異常検出アルゴリズム（anomaly detection）を用いて障害が発生したサーバーを検出、後半は映画おすすめシステムを作ります。

## 1. 異常検出

サーバーは２つのフィーチャーがあります：レスポンスのスループット(mb/s)とレイテンシ(ms)です。`m = 307 examples`のデータがあって、その中少し異常点があります。

### 1.1 正規分布

データ$x$は正規分布に従うのを仮定します。

正規分布：

$$
p(x;\mu,\sigma^2) = \frac{1}{\sqrt{2\pi\sigma^2}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
$$

ここで、$\mu$は平均値、$\sigma^2$は分散です。

### 1.2 正規分布のパラメータ推定

$i$番目のフィーチャーに対して：

* 平均値：

$$
\mu_ i = \frac{1}{m} \sum_ {j=1}^{m} x_ i^{(j)}
$$

* 分散：

$$
\sigma_ i^2 = \frac{1}{m}\sum_ {j=1}^{m}(x_ i^{(j)}-\mu_ i)^2
$$

Octaveで書き直すと、

```octave:estimateGaussian.m
mu = mean(X);
sigma2 = var(X) * (m - 1) / m;
```

注意するのはOctaveの分散計算関数(`var`)はデフォルトで`m`ではなく`m-1`を割るので工夫が必要です。

正規分布のグラフ（2Dで輪郭表現）は以下になります。
<img width="672" alt="Screen Shot 2016-04-16 at 22.14.21.png" src="https://galapagos.qiita.com/files/4e46365b-8a58-cd6a-e08f-df6b36ea59b7.png">

### 1.3 しきい値の選択

しきい値$\epsilon$の選択にはクロスバリデーションセットで$F_ 1$スコアの計算が必要です。

$F_ 1$はprecision ($prec$) とrecall ($rec$) から計算します。

$$
F_ 1 = \frac{2\cdot prec \cdot rec}{prec + rec}
$$

これで、



$$
prec = \frac{tp}{tp+fp}
$$

$$
rec = \frac{tp}{tp+fn}
$$


tp = number of true positives
fp = number of false positives
fn = number of false negatives

<table>
<tr>
<th>predicted</th>
<th>actual</th>
<th>名称</th>
</tr>
<tr>
<td>1</td>
<td>1</td>
<td>true positive</td>
</tr>
<tr>
<td>1</td>
<td>0</td>
<td>false positive</td>
</tr>
<tr>
<td>0</td>
<td>1</td>
<td>false negative</td>
</tr>
<tr>
<td>0</td><td>0</td><td>true negative</td>
</tr>
</table>

Octaveで書くと、

```octave:selectThreshold.m
predictions = (pval < epsilon);

% true positives
tp = sum(predictions & yval);
% false positives
fp = sum(predictions & !yval);
% false negatives
fn = sum(!predictions & yval);

prec = tp / (tp + fp);
rec  = tp / (tp + fn);

F1 = 2 * prec * rec / (prec + rec);
```

`predictions`は計算した結果（0と1からなる配列）、 `yval`は実際結果です。
以上のテーブルからANDとNOTの組み合わせで簡単にtp, fp, fnが計算できると思います。`sum`は個数を数えるためです。

<img width="672" alt="Screen Shot 2016-04-17 at 01.40.03.png" src="https://galapagos.qiita.com/files/59660d4d-d829-8968-7bf3-54849f692456.png">

赤い三角は異常点になります。

## 2. おすすめシステム

映画おすすめシステムを作ります。

まず、以下のデータがあります。
$n_ u$ = ユーザー数
$n_ m$ = 映画数
$r(i,j)$ = 1 if ユーザー$j$が映画$i$を評価した
$y(i,j)$ = 映画$i$に対しユーザー$j$によって評価点数

以下のパラメータを求めたいです。
$\theta(j)$ = ユーザー$j$のパラメータ（$n$次元ベクトル）
$x(i)$= 映画$i$のパラメータ（$n$次元ベクトル）

映画$i$に対しユーザー$j$によって評価点数の推定は $(\theta^{(j)})^T(x^{(i)})$になります。

### 2.1 コスト関数

正規化をまず考えずにコスト関数は以下の通りです。

$$
J_ {unregularized}\big(x^{(1)},\ldots,x^{(n_ m)},\theta^{(1)},\ldots,\theta^{(n_ u)}\big) = \frac{1}{2}\sum_ {(i,j):r(i,j)=1} \big((\theta^{(j)})^Tx^{(i)}-y^{(i,j)}\big)^2
$$

ベクトル化しつう、Octaveで書き直すと、こうなります。

```octave:cofiCostFunc.m
J = sum(sum(R .* (X*Theta' - Y) .^2)) / 2;
```
### 2.2 グラディエント

$$
\frac{\partial J}{\partial x_ k^{(i)}}=\sum_ {j:r(i,j)=1} \big((\theta^{(j)})^Tx^{(i)}-y^{(i,j)}\big)\theta_ k^{(j)}
$$
$$
\frac{\partial J}{\partial \theta_ k^{(j)}}=\sum_ {i:r(i,j)=1} \big((\theta^{(j)})^Tx^{(i)}-y^{(i,j)}\big)x_ k^{(i)}
$$

同様にベクトル化すると、

```octave:cofiCostFunc.m
X_ grad = R .* (X*Theta' - Y) * Theta; 
Theta_ grad = (R .* (X*Theta' - Y))' * X; 
```

### 2.3 コスト関数の正規化

$$
J = J_ {unregularized} + \Big(\frac{\lambda}{2}\sum_ {j=1}^{n_ u}\sum_ {k=1}^{n}(\theta_ k^{(j)})^2 \Big) + \Big(\frac{\lambda}{2}\sum_ {i=1}^{n_ m}\sum_ {k=1}^{n}(x_ k^{(i)})^2 \Big)
$$

Octaveで書くと、

```octave:cofiCostFunc.m
J += (sum(sum(Theta.^2)) + sum(sum(X.^2))) * lambda/2;
```

になります。

### 2.4 グラディエントの正規化

$$
\frac{\partial J}{\partial x_ k^{(i)}}=\sum_ {j:r(i,j)=1} \big((\theta^{(j)})^Tx^{(i)}-y^{(i,j)}\big)\theta_ k^{(j)} + \lambda x_ k^{(i)}
$$
$$
\frac{\partial J}{\partial \theta_ k^{(j)}}=\sum_ {i:r(i,j)=1} \big((\theta^{(j)})^Tx^{(i)}-y^{(i,j)}\big)x_ k^{(i)}+ \lambda\theta_ k^{(j)}
$$

Octaveで簡単に書けると思います。

```cofiCostFunc.m
X_ grad += lambda * X;
Theta_ grad += lambda * Theta;
```

### 2.5 自分へ映画おすすめの試す

映画リストは1682本がありますが、かなり古いです。その中見たやつを評価してみました。

```
New user ratings:
Rated 4 for Toy Story (1995)
Rated 5 for Star Wars (1977)
Rated 1 for Pulp Fiction (1994)
Rated 5 for Shawshank Redemption, The (1994)
Rated 5 for Forrest Gump (1994)
Rated 4 for Lion King, The (1994)
Rated 3 for Mask, The (1994)
Rated 5 for Aladdin (1992)
Rated 3 for Snow White and the Seven Dwarfs (1937)
Rated 5 for Godfather, The (1972)
Rated 5 for Empire Strikes Back, The (1980)
Rated 5 for Return of the Jedi (1983)
Rated 5 for Godfather: Part II, The (1974)
Rated 5 for Back to the Future (1985)
Rated 4 for Men in Black (1997)
Rated 3 for Titanic (1997)
Rated 3 for Casablanca (1942)
Rated 5 for Home Alone 3 (1997)
```

実行結果：

```
Top recommendations for you:
Predicting rating 9.0 for movie Star Wars (1977)
Predicting rating 8.6 for movie Raiders of the Lost Ark (1981)
Predicting rating 8.6 for movie Shawshank Redemption, The (1994)
Predicting rating 8.6 for movie Titanic (1997)
Predicting rating 8.6 for movie Empire Strikes Back, The (1980)
Predicting rating 8.5 for movie Schindler's List (1993)
Predicting rating 8.5 for movie Return of the Jedi (1983)
Predicting rating 8.3 for movie Braveheart (1995)
Predicting rating 8.2 for movie Good Will Hunting (1997)
Predicting rating 8.1 for movie Casablanca (1942)
```

おすすめ映画リストの中には見たやつと被っていて、少し残念です。改善の余地がありますね。

## 最後に...

これでCoursera終わりました！:tada:

