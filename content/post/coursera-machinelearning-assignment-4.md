+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-02-21T08:29:00Z
description = ""
draft = false
slug = "coursera-machinelearning-assignment-4"
tags = ["Machine Learning", "Japanese"]
title = "Coursera 機械学習 - プログラミング課題4解答例"

+++


解答例：https://github.com/vanhuyz/coursera-ml/tree/master/machine-learning-ex4/ex4

## 概要
今回の課題は前回と続き手書き数字の判定問題です。前回はニューラルネットワークで、与えられたパラメータからフィードフォワード・プロパゲイションを行うところまででした。今回はbackpropagationアルゴリズムを使って、パラメータを抽出することです（学習過程）。

## 1. ニューラルネットワーク
### 1.1 データを可視化

5000学習データがあり、各データは20x20ピックセルグレースケールの数字画像です。

<img width="408" alt="Screen Shot 2016-02-20 at 19.41.07.png" src="https://galapagos.qiita.com/files/0b4be25d-2fcd-bff9-2565-76c87570a54c.png">

### 1.2 モデル表現

ニューラルネットワークは３つのレイヤーがあります：input layer, hidden layerとoutput layerです。

<img width="478" alt="Screen Shot 2016-02-20 at 19.45.58.png" src="https://galapagos.qiita.com/files/1cc58d12-0384-a91b-5f8b-77ef03d9d31d.png">

### 1.3 フィードフォワードとコスト関数
ニューラルネットワークのコスト関数（未正規化）はこのように計算できます。

$$
J(\Theta) = \frac{1}{m}\sum_ {i=1}^{m}\sum_ {k=1}^{K}\Big[-y_ k^{(i)}\log((h_ \Theta(x^{(i)}))_ k)-(1-y_ k^{(i)})\log(1-(h_ \Theta(x^{(i)}))_ k)\Big]
$$

ここで$K=10$（10数字）、$m=5000$（学習データ）、$h_ \theta$はFigure 2から計算できます。$y$は0から9の数字ですが、計算都合のため以下の図のようにベクトルにマッピングします。

![Screen Shot 2016-02-20 at 22.28.59.png](https://galapagos.qiita.com/files/c3aac2b7-93d6-323c-5ba2-6a84f67a2436.png)

課題はコスト関数をOctaveで計算することです。

```octave:nnCostFunction.m
function [J grad] = nnCostFunction(nn_ params, ...
                                   input_ layer_ size, ...
                                   hidden_ layer_ size, ...
                                   num_ labels, ...
                                   X, y, lambda)
% Reshape nn_ params back into the parameters Theta1 and Theta2
Theta1 = reshape(nn_ params(1:hidden_ layer_ size * (input_ layer_ size + 1)), ...
                 hidden_ layer_ size, (input_ layer_ size + 1));

Theta2 = reshape(nn_ params((1 + (hidden_ layer_ size * (input_ layer_ size + 1))):end), ...
                 num_ labels, (hidden_ layer_ size + 1));

% Setup some useful variables
m = size(X, 1);
         
% You need to return the following variables correctly 
J = 0;
Theta1_ grad = zeros(size(Theta1));
Theta2_ grad = zeros(size(Theta2));

% ====================== YOUR CODE HERE ======================


% X          5000 x 400
% y          5000 x 1
% nn_ params 10285 x 1
% Theta1       25 x 401
% Theta2       10 x 26

% まずはyをYにマッピングする
Y = zeros(m,num_ labels);  % 5000 x 10
for i = 1:m 
  Y(i,y(i)) = 1;
end

% h_ thetaの計算。行列のサイズを確認しながら実装しましょう
A1 = [ones(m,1) X];             % 5000 x 401
Z2 = A1 * Theta1';              % 5000 x 25                   
A2 = [ones(m,1) sigmoid(Z2)];   % 5000 x 26
Z3 = A2 * Theta2';              % 5000 x 10
h = sigmoid(Z3);                % 5000 x 10

% 全部揃ったのでコスト関数をloopで計算
for i = 1:m
  for k = 1:num_ labels
    J += -Y(i,k) * log(h(i,k)) - (1-Y(i,k)) * log(1 - h(i,k));
  end
end

J = J/m;

% ==========================================
end
```

loopを使わないバージョン：

```octave
J = sum((-Y .* log(h) - (1-Y) .* log(1-h))(:));
```

`Y`,`h`はどれでも5000x10行列です。

それらをでかいベクトルに変換するとベクトル化もできます：

```octave
J = -Y(:)' * log(h(:)) - (1-Y(:))' * log(1-h(:));
```

### 1.4 コスト関数を正規化

以上の未正規化の関数を正規化部分を足します。

$$
J(\Theta) = J_ {unregularized}(\Theta) + \frac{\lambda}{2m}\Big[\sum_ {l=1}^{L-1}\sum_ {i=1}^{s_ l}\sum_ {j=1}^{s_ {l+1}} (\Theta_ {j,i}^{(l)})^2\Big]
$$

ここで、 $L$ はレイヤーの数、 $s_ l$ はレイヤー$l$のユーニットの数（バイアスユーニット(Figure 2の(+1)ユーニット)を含まない）。

ロジスティック回帰と同様、各Thetaの一番目のコラムを排除しないといけません。Octaveで書くと、

```octave:nnCostFunction.m
J += (sum(Theta1(:,2:end)(:).^2) + sum(Theta2(:,2:end)(:).^2)) * lambda / (2*m) ;
```

ここで`(:)`は行列をでかいベクトルに変換します(`sum`を使うためです）。

## 2.Backpropagation
今までのように、$J(\Theta)$の最小値を求めるには$\frac{\partial J(\Theta)}{\partial J(\Theta^{(l)}_ {i,j})}$を計算することが必要です。この偏微分を計算するには最強アルゴリズムがあります。それはbackpropagationです。

Backpropagationアルゴリズムを振り返します。
まずは中間的な値 $\delta_ j^{(l)}$ を導入します。 この値は $l$ 番目のレイヤーの $j$ 番目のユーニットの**誤差**と呼びます。

最終レイヤーの誤差は

$$
\delta^{(L)} = a^{(L)} - y
$$

になります。つまり、最終レイヤの誤差はただニューラルネットワークのoutputと実データ$y$の差です。最終レイヤの前レイヤーの誤差は以下のように計算できます（Ng先生より証明は難しいので省略）：

$$
\delta^{(l)} = ((\Theta^{(l)})^T\delta^{(l+1)} .* g'(z^{(l)})
$$

ここで $.*$ は各要素に対する掛け算です。$g'$ はsigmoid関数の導関数です。

$$
g(z) = \frac{1}{1+exp(-z)} 
$$

導関数を計算すると、

$$
g'(z) = g(z)(1-g(z)) 
$$

$a = g(z)$なので、

$$
\delta^{(l)} = ((\Theta^{(l)})^T\delta^{(l+1)} .* a^{(l)} .* (1-a^{(l)})
$$

になります。

![Screen Shot 2016-02-21 at 03.40.33.png](https://galapagos.qiita.com/files/059f49d9-20c2-d598-d4d4-b62044275080.png)


誤差の計算は最終レイヤから最初のhiddenレイヤーまで逆方向へ次々に計算するから、このアルゴリズムはbackpropagation(逆伝播)法と呼ばれます。

最後に、$t$ 番目の学習データに対して、偏微分は以下になります：

$$
\frac{\partial J(\Theta)}{\partial J(\Theta^{(l)}_ {i,j})}=\frac{1}{m}\sum_ {t=1}^{m}a_ j^{(t)(l)}\delta^{(t)(l+1)}_ i
$$

ここで、$\delta^{(t)(l)}_ i$は $t$ 番目の学習データに対して $l$ 番目のレイヤーの i 番目のノードの誤差です。記号はややこしいのでいつもFigure 2と3の図のように想像しながら公式を追いかけてください。


#### Backpropagationの実装ノート

* 学習データ：$(x^{(1)},y^{(1)})...(x^{(m)},y^{(m)})$
* Set $\Delta_ {i,j}^{(l)} := 0$ for all $(l,i,j)$
* 各学習データ $t = 1$ to $m$:
    * Set $a^{(1)} := x^{(t)}$
    * フォーワードプロパゲーションですべて$a^{(l)}$ for $l=2,3...L$を計算する
    * $y^{(t)}$を使って、$\delta^{(L)} = a^{(L)}-y^{(t)}$を計算する
    * $\delta^{(l)} = ((\Theta^{(l)})^T\delta^{(l+1)}).\*a^{(l)}.\*(1-a^{(l)})$より、
$\delta^{(L-1)},\delta^{(L-2)},...,\delta^{(2)}$を順序に計算する
    * $\Delta_ {i,j}^{(l)} := \Delta_ {i,j}^{(l)} + a^{(l)}_ j\delta_ i^{(l+1)}$ 
    * (またはベクトル化： $\Delta^{(l)} := \Delta^{(l)} + \delta^{(l+1)}(a^{(l)})^T$)
* 正規化の場合：

$$
D^{(l)}_ {i,j} := \begin{cases}
\frac{1}{m}(\Delta_ {i,j}^{(l)}+\lambda\Theta_ {(i,j)}^{(l)}) & \text{if}\quad j \neq 0\\
\frac{1}{m}\Delta_ {i,j}^{(l)} &\text{if}\quad j = 0
\end{cases}
$$

これで、

$$
D^{(l)}_ {i,j} = \frac{\partial J(\Theta)}{\partial J(\Theta^{(l)}_ {i,j})}
$$

は求めたかった偏微分です。

### 2.1 Sigmoid gradient

Sigmoid gradient（導関数）：

$$
g'(z) = \frac{d}{dz}g(z)=g(z)(1-g(z))
$$
Octaveになおせば、

```octave:sigmoidGradient.m
function g = sigmoidGradient(z)

g = zeros(size(z));
g = sigmoid(z) .* (1 - sigmoid(z));

end
```

になります。

### 2.2 Random initialization

ニューラルネットにはもし$\Theta$の初期化を全部0にしたら、うまく動きません。なぜなら、全部のユーニットは同じ計算なので全部同じになってしまいます。なので、ランダムで初期化が必要です。一般的に$[-\epsilon, \epsilon]$の間の乱数です。

```octave:randInitializeWeights.m
function W = randInitializeWeights(L_ in, L_ out)

epsilon_ init = 0.12;
W = rand(L_ out, 1 + L_ in) * 2 * epsilon_ init - epsilon_ init;

end
```

### 2.3 Backpropagation

loopのほうが複雑なので今回は行列バージョンだけです。

```octave:nnCostFunction.m
function [J grad] = nnCostFunction(nn_ params, ...
                                   input_ layer_ size, ...
                                   hidden_ layer_ size, ...
                                   num_ labels, ...
                                   X, y, lambda)

% feedforward 
% 1.3のコードなので略

% backpropagation

delta3 = h - Y;                                            % 5000 x 10
delta2 = (delta3*Theta2(:,2:end)) .* sigmoidGradient(Z2);  % 5000 x 25 

Delta1 = delta2' * A1; % 25 x 401
Delta2 = delta3' * A2; % 10 x 26

Theta1_ grad = Delta1/m;
Theta2_ grad = Delta2/m; 

% Unroll gradients
grad = [Theta1_ grad(:) ; Theta2_ grad(:)];

end
```

deltaは$\delta$、Deltaは$\Delta$に注意してください。
転置させたりさせなかったりがややこしいですがサイズを確かめながらいろいろ試したほうが簡単です。

### 2.4 Gradient checking
Backpropagationは強力ですが実装が難しく、間違っている可能性が高いです。そこで、Gradient checkingという手法があって、backpropagationの実装が正しいかどうかをチェックすることができます。課題はないので詳細は省略します。

### 2.5 Regularized Neural Networks

```octave:nnCostFunction.m
Theta1_ grad(:,2:end) += Theta1(:,2:end) * lambda/m;
Theta2_ grad(:,2:end) += Theta2(:,2:end) * lambda/m; 
```

### 2.6 Learning parameters using fmincg

コスト関数とgradientが実装できましたので、残りは`fmincg`に任せましょう。

* $\lambda = 1, MaxIter = 50$

```
Training Set Accuracy: 95.340000
```

* $\lambda = 0.1, MaxIter = 200$

```
Training Set Accuracy: 99.960000
```

よく学習データとフィットしていますね！過学習の恐れが。。。

## 3 ニューラルネットワークのまとめ

* inputユーニットの数 = フィーチャーの次元
* outputユーニットの数 = 分類したいクラスの数
* 各hiddenレイヤーのユーニットの数 = 多ければ多いほどよい
* デフォルト: 1つのhiddenレイヤー。１つ以上の場合、各hiddenレイヤーのユーニットの数は同じ
* ニューラルネットワークの学習
    * パラメタをランダムで初期化
    * フォーワードプロパゲーションで$h_ \theta(x^{(i)})$を計算
    * コスト関数を実装
    * Backpropagationで偏微分を計算
    * Gradient Checkingを使ってbackpropagationが上手く動くか確かめる
    * Gradient descentまたは高度な関数を使ってコスト関数の最小値になるパラメタを求める

## 4 もっと読みたい方へ

* 講義ノート：[https://share.coursera.org/wiki/index.php/ML:Neural_Networks:_Learning]()
* 逆伝播の仕組み：[http://nnadl-ja.github.io/nnadl_ site_ ja/chap2.html]()

