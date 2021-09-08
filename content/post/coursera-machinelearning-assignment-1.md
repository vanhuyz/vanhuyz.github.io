+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-01-18T13:07:00Z
description = ""
draft = false
slug = "coursera-machinelearning-assignment-1"
tags = ["Machine Learning", "Japanese"]
title = "Coursera 機械学習 - プログラミング課題1解答例"

+++


## 課題のページ
https://www.coursera.org/learn/machine-learning/programming/8f3qT/linear-regression

プログラミング課題はちょっと重いので今回の解答例を上げます。
わからないことや別の解答がありましたらコメントをお願いします。

## 必須課題
### 1. Computing Cost (for One Variable)

Gradient DescentのCost function $J(\theta)$ は以下の通り

$$
J(\theta) = \frac{1}{2m}\sum_ {i=1}^m (h_ \theta(x^{(i)}) - y^{(i)} )^2
$$

ここで、仮定関数 $ h_ \theta(x) $は

$$
h_ \theta(x) = \theta^T x = \theta_ 0 + \theta_ 1x_ 1
$$

それで、

$$
J(\theta) = \frac{1}{2m}\sum_ {i=1}^m (\theta^T x^{(i)} - y^{(i)} )^2
$$

注意したいのは $\theta$ と $x^{(i)}$ はベクトルで、$ y^{(i)} $は実数です。
課題はこの関数をOctaveで書くことです。以下は解答例です。

```octave:computeCost.m
function J = computeCost(X, y, theta)

m = length(y); % number of training examples
% 97 examplesがあるのでここで m == 97

% size(X) == [97 2]
% size(y) == [97 1]
% size(theta) == [2 1]

% Xは97x2行列。１行は１つのtraining example [x0 x1]　(x0はいつも1)
% yは97次元ベクトル
% thetaは２次元ベクトル

J = 0;

% まずは和の部分を計算します
for i = 1:m
  J += (theta' * X(i,:)' - y(i))^2;
end

% X(i,:)は１つの行、つまりtraining exampleです。ベクトルに変換するので転置を取りました。

% 最後に2mを割るだけです
J = J / (2*m);

end
```

実行した結果、cost functionの値は32.073になるはずです。

## 2. Gradient Descent (for One Variable)

Gradient descentアルゴリズムはcost function Jの最小値を求める手法の一つです。一つのステップは以下のように$\theta$の値を更新します

$$
\theta_ j := \theta_ j - \alpha\frac{1}{m}\sum_ {i=1}^{m} ( h_ \theta(x^{(i)}) - y^{(i)} )x_ j^{(i)}
$$

ベクトル化すると、

$$
\theta := \theta - \alpha\delta
$$

ここで、

$$
\delta = \frac{1}{m}\sum_ {i=1}^{m} ( \theta ^Tx^{(i)}- y^{(i)} )  x^{(i)}
$$

注意したいのは$\delta,x^{(i)}$はベクトルで、$ \theta ^Tx^{(i)}- y^{(i)}$は実数です。
課題はOctaveでgradient descentによって最小値をとる$\theta$を求めることです。以下は解答例です。

```octave:gradientDescent.m
function [theta, J_history] = gradientDescent(X, y, theta, alpha, num_ iters)

m = length(y); % number of training examples
J_history = zeros(num_ iters, 1);

for iter = 1:num_ iters

    % deltaを初期化
    delta = zeros(2,1);
    for i = 1:m
        x = X(i,:)';
        delta += (theta' * x - y(i)) * x;
    end

    delta = delta / m;
    theta = theta - alpha * delta;

    J_history(iter) = computeCost(X, y, theta);
end

end

```

実行した結果：

```
Theta found by gradient descent: -3.630291 1.166362
For population = 35,000, we predict a profit of 4519.767868
For population = 70,000, we predict a profit of 45342.450129
```

## 任意課題
### 3. Feature Normalization

j番目のフィーチャのi番目のtraining exampleに対して

$$
x_ j^{(i)} := \frac{x_ j^{(i)} - \mu_ j}{\sigma_ j}
$$

ここで、$\mu_ j$はjフィーチャの平均値、$\sigma_ j$はjフィーチャの標準偏差です。

Octaveで書くと、

```octave:featureNormalize.m
function [X_norm, mu, sigma] = featureNormalize(X)

X_norm = X;
mu = zeros(1, size(X, 2));
sigma = zeros(1, size(X, 2));

% size(X) == [47 2]
% size(mu) == [1 2]
% size(sigma) == [1 2]

mu = mean(X);
sigma = std(X);

% X_norm = (X - mu) ./ sigma; 
% これも結果正しいですが、実行すると
% warning: operator -: automatic broadcasting operation applied

% warningを出ないようにするならbsxfunを使う
X_norm = bsxfun(@rdivide, bsxfun(@minus, X, mu), sigma);

% またはXのサイズに合わせる
% o = ones(size(X,1),1);
% X_norm = (X .- o*mu ) ./ (o*sigma);

end
```

参考：http://stackoverflow.com/questions/17094753/octave-operator-automatic-broadcasting-operation-applied

### 4. Computing Cost (for Multiple Variables)

複数変数の場合、cost functionは以下のようになります（だれか証明してくれませんか？）

$$
J(\theta) = \frac{1}{2m}(X\theta-y)^T(X\theta-y)
$$

Octaveで書くと、

```octave:computeCostMulti.m
function J = computeCostMulti(X, y, theta)
m = length(y); % number of training examples
J = 0;

J = 1 / (2*m) * (X*theta - y)' * (X*theta - y);

end
```

### 5. Gradient Descent (for Multiple Variables)
１変数の場合と同様です。違うのは$\delta$の次元は2ではなくフィーチャ数+1となります。だから、上のコード（１変数の時）を$\delta$の初期化の行だけ変更すれば動くはずです。

```octave:gradientDescentMulti.m
 delta = zeros(size(X, 2),1);
```

それから、面積1650 square feet、3つのルームの値段を予測には

```octave:ex1_ multi.m
xt = [1650 3];
% 値段を計算する前にxtをnormalizeすることが必要
price = [1 (xt-mu)./sigma ] * theta; 
``` 

実行結果：

```
Normalizing Features ...
Running gradient descent ...
Theta computed from gradient descent:
 334302.063993
 100087.116006
 3673.548451

Predicted price of a 1650 sq-ft, 3 br house (using gradient descent):
 $289314.620338
```

### 6. Normal Equations

$\theta = (X^TX)^{-1}X^Ty$をOctaveで書くと、

```octave:normalEqn.m
function [theta] = normalEqn(X, y)

theta = zeros(size(X, 2), 1);

theta = pinv(X' * X) * X' * y;

end
```

面積1650 square feet、3つのルームの値段を予測にはfeature normalizeが必要ないので

```octave:ex1_ multi.m
price = [1 1650 3] * theta; 
``` 

実行結果：

```
Solving with normal equations...
Theta computed from the normal equations:
 89597.909542
 139.210674
 -8738.019112

Predicted price of a 1650 sq-ft, 3 br house (using normal equations):
 $293081.464335
```

### 発展課題：Gradient Descentのlearning rateの考察

Gradient Descentの結果はNormal Equationsとちょっと違うので、learning rate $\alpha$ の調整の余地があります。

現在は$\alpha = 0.01$、ステップの回数は400です。

* $\alpha = 0.03, num\\_ iters = 400$

```
Theta computed from gradient descent:
 340410.918973
 110308.113371
 -6326.538108

Predicted price of a 1650 sq-ft, 3 br house (using gradient descent):
 $293149.994329
```
* $\alpha = 0.1, num\\_ iters = 400$

```
Theta computed from gradient descent:
 340412.659574
 110631.048958
 -6649.472950

Predicted price of a 1650 sq-ft, 3 br house (using gradient descent):
 $293081.464622
```

Cost Jのグラフ：

![alphas.png](https://galapagos.qiita.com/files/af232f4a-7d67-da62-d257-deb1cbbbda89.png)

グラフを見ればわかるように$\alpha = 0.1$のとき, ステップ数は50だけでも良さそうです。

もっと$\alpha$を上げてみると、$\alpha=3$のときは発散することがわかります。

![alphas_ big.png](https://galapagos.qiita.com/files/8d1607a8-7bb2-0bae-e9a6-0a5a4450c6fa.png)

