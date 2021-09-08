+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-01-31T15:38:00Z
description = ""
draft = false
slug = "coursera-machinelearning-assignment-2"
tags = ["Machine Learning", "Japanese"]
title = "Coursera 機械学習 - プログラミング課題２解答例"

+++


## 課題について
今回の課題はLogistic Regression（分類）に関するものです。<br>
https://www.coursera.org/learn/machine-learning/programming/ixFof/logistic-regression

ソースコードはgithubに上げました。<br>
https://github.com/vanhuyz/coursera-ml/tree/master/machine-learning-ex2/ex2

## 問題１

Logistic regressionモデルで学生の過去の２つテスト結果から大学入試に合格・不合格を予測します。

学習データは以下の図の通りです。＋は合格、◯は不合格です。

![figure1.png](https://galapagos.qiita.com/files/15856025-cb5d-39cd-7989-69ca92877271.png)

### 1. Sigmoid関数

Sigmoid関数は以下のように定義されています。

$$
g(z)=\frac{1}{1+e^{-z}}
$$

注意したいのはzはスカラー、ベクトル、行列でもいいです。ベクトル・行列の場合は各要素を適応することになるます。


実装：

```octave:sigmoid.m
function g = sigmoid(z)
    g = zeros(size(z));
    g = 1 ./ (1 + exp(-z));
end
```

Octaveの**+**、**exp**演算子はもし行列と実数を計算するとき、自動に各要素に適応するようです。　**/**　はそうになっていないのでちゃんと　**.**　をつけましょう。

### 2. Cost function and gradient

* Cost function:

$$
J(\theta) = \frac{1}{m}\sum_ {i=1}^{m}[-y^{(i)}\log(h_ \theta(x^{(i)}))-(1-y^{(i)})\log(1-h_ \theta(x^{(i)}))]
$$

* Gradient:

$$
\frac{\partial J(\theta)}{\partial \theta_ j} = \frac{1}{m}\sum_ {i=1}^m (h_ \theta(x^{(i)})-y^{(i)})x_ j^{(i)}
$$

実装：
理解しやすいためcost functionの計算とgradientの計算を分けています。

```octave
function [J, grad] = costFunction(theta, X, y)

m = length(y); % number of training examples

J = 0;
grad = zeros(size(theta));

% cost function
for i = 1:m
    h = sigmoid(X(i,:) * theta);
    J += -y(i)*log(h) - (1 - y(i))*log(1-h);
end

J = J / m;

% gradient
for j = 1:size(theta)
    for i = 1:m
        h = sigmoid(X(i,:) * theta);
        grad(j) += (h - y(i)) * X(i,j);
    end
end

grad = grad / m;

end
```

#### 番外編：Gradientのベクトル化：
gradientベクトルを$\nabla$で記号すると、以下のようになります（予想です。僕がまだ証明しません。でも結果は正しいので合っているでしょう）

$$
\nabla J(\theta) = \frac{1}{m}X^T(g(X\vec \theta)-\vec y)
$$

実装は簡単になり、速度も速くなります。

```octave
grad = X'* (sigmoid(X*theta) - y) / m
```

### 3. Learning parameters using fminunc

課題はありません。実装コードはすでに提供されます。

```octave:costFuction.m
%  Set options for fminunc
options = optimset('GradObj', 'on', 'MaxIter', 400);

%  Run fminunc to obtain the optimal theta
%  This function will return theta and the cost 
[theta, cost] = fminunc(@(t)(costFunction(t, X, y)), initial_ theta, options);
```

`@`部分はRubyのProc/lambdaみたいなものだと思います。

#### 実行結果

```bash
Cost at initial theta (zeros): 0.693147
Gradient at initial theta (zeros):
 -0.100000
 -12.009217
 -11.262842

Cost at theta found by fminunc: 0.203498
theta:
 -25.161272
 0.206233
 0.201470

For a student with scores 45 and 85, we predict an admission probability of 
0.776289
```

![figure2.png](https://galapagos.qiita.com/files/3c1201ed-29fa-ee07-5c54-f431e496df68.png)
 


### 4.Evaluating logistic regression

求められた$\theta$の値を使って、学習データを再評価します（どのぐらい合っているのかを計算します）

```octave:predict.m
function p = predict(theta, X)
%PREDICT Predict whether the label is 0 or 1 using learned logistic 
%regression parameters theta
%   p = PREDICT(theta, X) computes the predictions for X using a 
%   threshold at 0.5 (i.e., if sigmoid(theta'*x) >= 0.5, predict 1)

m = size(X, 1); % Number of training examples

p = zeros(m, 1);

for i = 1:m
    h = sigmoid(X(i,:) * theta);
    if h >= 0.5
        p(i) = 1;
    end
end

end
```

別の方法：loopを使わずに一括計算するとこうなります

```
p = round(sigmoid(X*theta));
```

`round`関数は0.5以上の時は１になり、0.5未満の時は0になります。

実行結果：

```
Train Accuracy: 89.000000
```

## 問題２

２つのテストからマイクロチップの質量を判定するプログラムです。
学習データは以下です(y=0はNG、y=1はOK）。

![figure3.png](https://galapagos.qiita.com/files/2f78a098-0c22-fbae-c2ed-5e7bc80aee52.png)

### フィーチャ

6次フィーチャを想定します

```
mapFeature(x) = \left[
    \begin{array}{c}
      1 \\
      x_ 1 \\
      x_ 2 \\
      x_ 1^2 \\
      x_ 1x_ 2 \\
      x_ 2^2 \\
      x_ 1^3 \\
      ... \\
      x_ 1x_ 2^5 \\
      x_ 2^6
    \end{array}
  \right]
```

### Cost function and gradient

* Cost function 

$$
J(\theta) = \frac{1}{m}\sum_ {i=1}^{m}[-y^{(i)}\log(h_ \theta(x^{(i)}))-(1-y^{(i)})\log(1-h_ \theta(x^{(i)}))] + \frac{\lambda}{2m}\sum_ {j=1}^{n}\theta_ j^2
$$

* Gradient

$j=0$のとき

$$
\frac{\partial J(\theta)}{\partial \theta_ j} = \frac{1}{m}\sum_ {i=1}^m (h_ \theta(x^{(i)})-y^{(i)})x_ j^{(i)}
$$

$j\ge1$のとき

$$
\frac{\partial J(\theta)}{\partial \theta_ j} = \frac{1}{m}\sum_ {i=1}^m (h_ \theta(x^{(i)})-y^{(i)})x_ j^{(i)} +\frac{\lambda}{m}\theta_ j
$$

注意したいのは正規化のパラメータは0からではなく、**1から**です。Octaveで書くと2からになります。

$\sum_ {j=1}^{n}\theta_ j^2$はloopで計算するのもいいですが、よく見たら$\theta$ベクトル（$\theta_ 0$を除き）のノルムの２乗です。Octaveは`norm`関数があるので、実装に便利です。

実装：

```octave:costFuntionReg.m
function [J, grad] = costFunctionReg(theta, X, y, lambda)

m = length(y); % number of training examples

J = 0;
grad = zeros(size(theta));



% Cost function
for i = 1:m
    h = sigmoid(X(i,:) * theta);
    J += -y(i)*log(h) - (1 - y(i))*log(1-h);
end

J = J / m + norm(theta(2:size(theta)))^2 * lambda / (2*m);


% Gradient
for j = 1:size(theta)
    for i = 1:m
        h = sigmoid(X(i,:) * theta);
        grad(j) += (h - y(i)) * X(i,j);
    end
    if j > 1
      grad(j) += lambda * theta(j);
    end
end

grad = grad / m;

end
```

#### 番外編：Gradientのベクトル化バージョン

$j=0$と$j\ge1$を分けて計算します。

実装：

```
n = size(theta);

s = (sigmoid(X * theta) - y) / m;
grad(1) = X(:,1)' * s;
grad(2:n) = X(:,2:n)' * s + lambda * theta(2:n) / m;
```

### 結果

問題１と同じく`fminunc`を使って結果は以下になります。

![figure4.png](https://galapagos.qiita.com/files/c6a73e93-cb49-61df-74c2-e23e170fa83f.png)

```bash
Train Accuracy: 83.050847
```

