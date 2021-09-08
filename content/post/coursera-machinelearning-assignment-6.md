+++
author = "Huy Van"
categories = ["Machine Learning", "Japanese"]
date = 2016-03-14T03:05:00Z
description = ""
draft = false
slug = "coursera-machinelearning-assignment-6"
tags = ["Machine Learning", "Japanese"]
title = "Coursera 機械学習 - プログラミング課題6解答例"

+++


ソースコード：https://github.com/vanhuyz/coursera-ml/tree/master/machine-learning-ex6/ex6

## 0. 概要
今回の課題はSupport Vector Machines(SVM)を使って迷惑メールを識別することです　(spam classifier)。パート１はSVMをいろいろ試すのと、パート２は迷惑メールの課題になります。

## 1. Support Vector Machines (SVM)
### SVM with Gaussian Kernels
Gaussian Kernel:

$$
K_ {gaussian}(x^{(i)},x^{(j)}) = \exp\Big(-\frac{\|x^{(i)}-x^{(j)}\|^2}{2\sigma^2}\Big) = \exp\Bigg(-\frac{\sum_ {k=1}^n(x_ k^{(i)}-x_ k^{(j)})^2}{2\sigma^2}\Bigg)
$$

Octaveで書き直すと、

```octave:gaussianKernel.m
function sim = gaussianKernel(x1, x2, sigma)
    x1 = x1(:); x2 = x2(:);
    sim = exp(-norm(x1 - x2)^2 / (2*sigma^2));
end
```

途中で実行するエラーがありました

```
error: get: unknown hggroup property Color
```

`visualizeBoundary.m` ファイルの21行
```
contour(X1, X2, vals, [1,1], 'b')
```
に変更するとなおりました。

参考：https://www.coursera.org/learn/machine-learning/discussions/1RmNir4KEeWSBRJpSArseQ

データセット２の実行結果：
<img width="636" alt="Screen Shot 2016-03-13 at 00.28.55.png" src="https://galapagos.qiita.com/files/eb0cddc0-6f3e-e5fc-b346-4d120bc31af4.png">

### Cross Validation Setでパラメータ選択
SVMのライブラリを使う前提なのでSVMの中身は気にしなくていいです。SVM with a Gaussian kernelを使う場合、調整必要なパラメータは$C$と$\sigma$だけです。$C$は正規化を調整するパラメータで、$\sigma$は２つ点の類似度（similarity)を調整するパラメータです。
一般的に,
<table>
<thead>
<tr>
<th>$C$</th>
<th>$\sigma$</th>
<th>結果</th>
</thead>
<tbody>
<tr>
<td> 大きい </td>
<td> 小さい </td>
<td> higher variance/lower bias </td>
</tr>
<tr>
<td>小さい</td>
<td>大きい</td>
<td>lower variance/higher bias</td>
</tr>
</tbody>
</table>

課題はcross validation setでたくさんの$C$と$\sigma$のペアの値を試して、エラーが一番小さいペアを求めることです。値は(0.01, 0.03, 0.1, 0.3, 1, 3, 10, 30)を試すと良いでしょう。

以下は実装です。Plotしたいため全部の計算結果を保存します。

```octave:dataset3Params.m
function [C, sigma] = dataset3Params(X, y, Xval, yval)

C = 1;
sigma = 0.3;

C_vec = sigma_vec = [0.01 0.03 0.1 0.3 1 3 10 30]';
errors = zeros(length(C_vec));

for i = 1:length(C_vec)
  for j = 1:length(sigma_vec)
    model= svmTrain(X, y, C_vec(i), @(x1, x2) gaussianKernel(x1, x2, sigma_vec(j)));
    predictions = svmPredict(model, Xval);
    errors(i,j) = mean(double(predictions ~= yval));
  end
end

[dummy,I] = min(errors(:));
[I_row,I_col] = ind2sub(size(errors),I);

C = C_vec(I_row);
sigma = sigma_vec(I_col);

% =========================================================================
% plot
p = zeros(length(sigma_vec),1);
colors = ['k','r','g','b','m','c','k','r'];
styles = ['-','-','-','-','-','-','--','--'];
for j = 1:length(sigma_vec)
  p(j) = semilogx(C_vec, errors(j,:),'color',colors(j),'linestyle',styles(j));
  legend(p(j),sprintf("%0.2f",sigma_vec(j)));
  hold on;
end
xlabel('C');
ylabel('Error');
title('sigma')
hold off;


end

```

<img width="636" alt="Screen Shot 2016-03-13 at 04.14.09.png" src="https://galapagos.qiita.com/files/96e5e97e-1799-3f20-409d-6392101968b4.png">


`C = 1, sigma = 0.1`の時エラーが一番小さいのがわかります。
このパラメータを採用して、テストデータで実行します：

<img width="631" alt="Screen Shot 2016-03-13 at 19.58.14.png" src="https://galapagos.qiita.com/files/04061f46-7686-a4c3-8a64-d3aa8fa196c4.png">


## 2. Spam Classification
練習問題ができましたので、実際問題に入ります。迷惑メールを識別には以下の手順で行います。

### 流れ
#### Step 1：メールの前処理

* 小文字に変換： 全部の単語を小文字に変換します。その結果、例えば, IndIcaTEとIndicateは同じ単語として扱います
* HTMLタグを排除： メールの内容だけ残します。
* URLを正規化： 全部のURLを 「httpaddr」に入れ帰ります。
* メールアドレスを正規化：同じくメールアドレス　→ 「emailaddr」
* 数字を正規化：数字　→　「number」
* ドル記号を正規化：$　→　「dollar」
* ワード・ステミング：　各ワードを原始型に変換します。例えば「discounts」、「discounted」、 「discounting」→　「discount」。時々、原始型は辞書に載っていない、例えば「include」→　「includ」。まぁ、問題無いでしょう！
* 非言葉を排除：非言葉（単語ではない）をすべて排除します。タブ、改行、複数のスペースを１つのスペースに変換します。

#### Step 2：単語リストの用意

メールの前処理ができまして、次のステップはその中にどの単語を使うべきかを検討します。全部を使ったら、過学習になりがちので、もっとも使われている単語のみを考慮します。今回は1899単語のリストが用意されました。実際には10000~50000単語らしいです。

#### Step 3：メールからフィーチャーを抽出する
例えば、単語リストは以下です：
<img width="189" alt="Screen Shot 2016-03-13 at 20.48.56.png" src="https://galapagos.qiita.com/files/fafa34f7-c2a2-6834-9e21-306e3ac0af4f.png">

メールを前処理を行ったあと、単語リストから参照してメールを単語インデックスリストに変換することができます。

<img width="243" alt="Screen Shot 2016-03-13 at 20.52.55.png" src="https://galapagos.qiita.com/files/af72ed7a-fc96-64dc-f1ad-d4507e58ed76.png">

次に、このインデックスリストからフィーチャーベクトルを作ります。フィーチャーベクトル$x$は0と1のみもつベクトルで、もし単語リストの$i$番目の単語がメールに含まれるなら$x_i=1$、含まれないなら$x_i=0$。

その結果、一つのメールは以下のベクトルに相当します。

<img width="146" alt="Screen Shot 2016-03-13 at 21.03.41.png" src="https://galapagos.qiita.com/files/affd9922-43a0-9990-4508-1abaf0146f23.png">

単語リストは1899個があるなら$n=1899$になりますね。

#### Step 4：SVMを使う

フィーチャーができたので普通にSVM使います。

### 課題
今回の課題はStep 3のみです。
まず、インデックスリストを作ります。`vocabList`から探して、見つけたら`word_indices`に追加します。

```processEmail.m
str_idx = find(strcmp(vocabList, str));
word_indices = [word_indices; str_idx];
```

次はインデックスリストからフィーチャーベクトルに変換します。

```emailFeatures.m
for i = 1:length(word_indices)
  x(word_indices(i)) = 1; 
end
```

残りはSVMに食わせて、ズドン！と完了です。

