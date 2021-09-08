+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-12-20T06:51:00Z
description = ""
draft = false
slug = "maximum-likelihood-examples"
tags = ["Machine Learning", "Japanese"]
title = "最尤推定は何ができるの？"

+++


英語: Maximum Likelihood Estimation (MLE)

最近仕事で確率モデルを扱う機会があって、パラメータ推定には最尤推定を使うことがありました。
でもわかるような、わからないような状態なので、式を立てて一度整理したいと思います。

## 例１
### 問題
データ $D= \{ x^{(1)},..., x^{(N)} \}$（母集団） が与えられるとします。
このデータが正規分布に従うと仮定したら、最尤推定でパラメータを推定しましょう。

### 回答
まず正規分布の式です。

$$
p(x) = \frac{1}{\sqrt{2\pi\sigma^2}}\exp \big(-\frac{(x-\mu)^2}{2\sigma^2}\big) 
$$

最尤推定はとは、尤度がもっとも高くなるようにパラメータを決定する方法です。「できるかぎりデータにフィットさせる」推定方法です[^2]。

最尤推定の1つ目の条件は、データは**独立に同一の確率分布**(i.i.d)[^1]に従うのです。
そこで、尤度(likelihood)は

$$
p(D) = \prod_ {x^{(i)}\in D} p(x^{(i)})
$$

です。これを**最大化**したいです。

確率$p$は$[0,1]$の間の値なので、積をとるとコンピュータの計算に誤差が出るので実際、$\log$[^3]をとることが多いです。

$$
\begin{align}
\log p(D) &= \sum_ {x^{(i)}\in D} \log p(x^{(i)}) \\\
&= \sum_ {x^{(i)}\in D} \log \Big(\frac{1}{\sqrt{2\pi\sigma^2}}\exp \big(-\frac{(x^{(i)}-\mu)^2}{2\sigma^2}\big)\Big) \\\
&= -\frac{N}{2}\log(2\pi) - N\log (\sigma) - \sum_ {x^{(i)}\in D} \frac{(x^{(i)}-\mu)^2}{2\sigma^2}
\end{align}
$$

この場合のパラメータは$(\mu, \sigma)$ですね。

$$
\begin{align}
L(\mu, \sigma) &= -\log P(D) \\\
&= \frac{N}{2}\log(2\pi) + N\log (\sigma) + \sum_ {x^{(i)}\in D} \frac{(x^{(i)}-\mu)^2}{2\sigma^2}
\end{align}
$$

とします。Lはnegative log likelihoodとも言います。尤度の最大化はLの最小化と等価です。
最小化問題なので微分を計算します。

$$
\begin{align}
\frac{\partial L}{\partial \mu} &=  \sum_ {x^{(i)}\in D} \frac{\mu-x^{(i)}}{\sigma^2} \\\
\frac{\partial L}{\partial \sigma} &= \frac{N}{\sigma} - \sum_ {x^{(i)}\in D} \frac{(x^{(i)}-\mu)^2}{\sigma^3}
\end{align}
$$

偏微分を全部0にすると、

$$
\mu = \frac{1}{N} \sum_ {x^{(i)}\in D} x^{(i)} \\\
\sigma^2 = \frac{1}{N} \sum_ {x^{(i)}\in D} (x^{(i)}-\mu)^2
$$

すなわち、$\mu$はデータDの平均、$\sigma$はデータDの標準偏差となっています。
正規分布関数は凹関数なので、この値におけるLは最小値になります。

## 例２

### 問題
最尤推定でunigramモデルを作りましょう。

### 回答

すなわち、各単語$w_ i$の確率$p_ i$を求めることです。
ここで$N$は全単語の数で、単語$w_ i$の数は$n_ i$とします。直感的には

$$
p_ w = \frac{n_ w}{N}
$$

でしょうが、実際最尤推定で求めましょう。

対数尤度(log likelihood)は

$$
\log p(D) = \sum_ {w_ i \in D} n_ i \log(p_ i)
$$
となります。
今回は分布のパラメータを求めるのではなく、確率$p_ i$を求めることです。
対数尤度を最大化するには微分を計算してみます。

$$
\frac{\partial \log p(D)}{\partial p_ i} = \frac{n_ w}{p_ w}
$$

これを0にすると$p_ w$は∞になりますが、注意したいのは$\sum_ {w_ i \in D} p_ i = 1$という制限があります。

なので、別の方法を考えましょう。制限付きの最適化問題にはラグランジュ乗数法があります。

ラグランジュ関数$L(p,\lambda)$を作ります。

$$
L(p,\lambda) =  \sum_ {w_ i \in D} n_ i \log(p_ i) + \lambda(\sum_ {w_ i \in D} p_ i - 1)
$$
各パラメータ$p_ w$に関しての偏微分を計算すると、

$$
\frac{\partial{L(p,\lambda)}}{\partial {p_ w}}=\frac{n_ w}{p_ w}+\lambda
$$

全部0にしたら、

$$
-\lambda = \frac{n_ w}{p_ w} = \frac{\sum_ {w_ i \in D} n_ w}{\sum_ {w_ i \in D}p_ w}=\frac{N}{1}=N
$$

になります。
結果:

$$
p_ w = \frac{n_ w}{N}
$$

直感的に合っていますね。

## 最尤推定の問題点

最尤推定は「できるかぎりデータにフィットさせる」推定方法なので、求めた解はoverfitすることが多いそうです。
例えば、unigramモデルの場合は、未知語の確率は全部0になってしまいます。
これは実際問題に応用するときには注意することが必要です。

[^1]: independently identically distributed
[^2]: なぜ尤度が最大のときデータにフィットするのかここには説明しません
[^3]: ここの$\log$は実質$\log_ e$です。

