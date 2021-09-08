+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T11:16:01Z
description = ""
draft = true
slug = "udacity-deeplearning-lecture-note-6"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - 講義ノート6"

+++


# Sequences of Varying Length
word2vecにより単語のモデルができました。次はテキスト（つまり単語のシーケンス）をモデル化しましょう。
今まで勉強したのは、入力いつもが固定サイズでした。固定サイズというのは入力をベクトル化できて、そのベクトルをニューラルネットワークに入れることができるとのことです。でも、テキストや音声などは固定サイズではなく可変長なシーケンスです。どうします？

## RNNs (Recurrent Neural Networks)
CNN (畳み込みニューラルネットワーク）は空間的共有重み[^1]により画像のパータンを抽出しました。
今イベントのシーケンスがあって、各時点で何が起こったのかを決定したい場合は、同じ共有重みのアイデアで、**時間的共有重み**も考えられます。例えば、音声認識問題について考えましょう。音声認識は音声のシーケンスからテキストのシーケンスに変換するものです。１つの音声のシーケンスは同じ話者・同じ言語で・同じ雑音環境など、いろんな共有性質があります。そのため、共有重みを適応できると、パラメータの数を激減できて、シーケンスの性質も抽出できるでしょう。

![image](https://galapagos.qiita.com/files/06f1985c-3b63-6213-a6b9-bea272391431.png)


また、シーケンスなので過去のものが現在のものに影響を与えることが普通です。
１つ目の考え方は全部過去の入力を現在のモデルを入れることです（下の図の青線）。

数式で表してみます。シーケンス $x^{(1)},x^{(2)},x^{(3)},...,x^{(\tau)}$ があって、時刻$t$におけるステートは$h^{(t)}$を記号します。

現在のステートは今までの入力の関数ですから、

$$
h^{(t)} = g^{(t)}\big(x^{(t)},x^{(t-1)},...,x^{(1)}\big)
$$

で表現できます。
但し、この表現の仕方はパラメータの長さは可変になってしまうため計算に困ります。
ここで気づいていたことは

$$
h^{(t-1)} = g^{(t-1)}\big(x^{(t-1)},...,x^{(1)}\big)
$$

になりますから、現在のステート$h^{(t)}$は直前のステート$h^{(t-1)}$と現在の入力$x^{(t)}$の関数となりますね。

$$
h^{(t)} = f^{(t)}\big(h^{(t-1)},x^{(t)};\theta\big)
$$

この表現で、パラメータは固定サイズになりまして、計算が可能になります。

![image](https://galapagos.qiita.com/files/33ab077a-e988-a76b-59f9-18d94bb6e4a6.png)

関数$f^{(t)}$は遷移関数になり、また時間的共有重みの考えで、その関数を統一させることができます。
その結果、１つのモデルで過去の情報のまとめができ、現在の情報も表すことができるとのことです。

$$
h^{(t)} = f\big(h^{(t-1)},x^{(t)};\theta\big)
$$

![image](https://galapagos.qiita.com/files/db37821a-d99d-334d-6038-bbbf7e6e409e.png)

以上はRNNの基本な考え方です。

## Backprop Through time

RNNの勾配計算には普通の逆伝播 (backpropagation)を使います。違うのはRNNには時間があるから、逆時間に沿っての計算が必要です。これはbackpropagation through time (BPTT)と呼びます。ファンシーな呼び方ではありますが普通のbackpropとあまり変わらないです。違うのは前のステートを考慮することだけです。

![image](https://galapagos.qiita.com/files/7545bfc3-827c-bd4e-8a43-f37353f79e9d.png)

RNNは勾配消失/爆発が起こりやすいです。

勾配消失/爆発はディープニューラルネットワークの古典な問題で、深ければ深いほど逆転番が前のレイヤに届けにくくなります。
RNNに起こりやすいのはもう一つの原因があって、それは**どのレイヤでも同じ重み**です。
ざっくり説明すると、まず重み$W$を自分自身何回も掛けることを想像しましょう。$t$ステップの後、$W^t$になります。行列のn乗の計算は大学の線形代数を思い出してみましょう[^5]（まだ学んでいない方は飛ばしてもいいです）。

まず$W$を固有分解すると、

$$
W = V\times diag(\lambda) \times V^{-1}
$$

各$\lambda_ i$は固有値で、$diag(\lambda)$は固有値から構成された対角行列です。これで、

$$
W^t = V\times diag(\lambda)^t \times V^{-1}
$$

になります。
$\lambda_ i < 1$の場合 $\lambda_ i^t$ が消失になり、$\lambda_ i > 1$の場合 $\lambda_ i^t$ が爆発になります[^4]。
結局、$W$が消失/爆発になったり、$Grad(W)$も消失/爆発になるわけです。(勾配の計算はややこしいのでここで避けたいですが、最終的の誤差は$W^t$項が入っています。詳しく知りたい人は参考資料[^3]をどうぞ！)

![image](https://galapagos.qiita.com/files/56b0c064-c46b-2980-5622-99e314fecd5e.png)


## Vanishing/Exploding Gradients
（勾配消失/爆発問題についてはこの論文[^2]が詳しいです。）

さて、勾配消失/爆発問題はどうやって対応しますか。
まず、勾配爆発の対応はGradient Clippingという技があります。単純に勾配の最大値に制限する技です。例えば、勾配は100は大きすぎるから5にしましょうという超単純な考えです。

最大値$\Delta_ {max}$の値を決めて、もし勾配$\Delta w$のノルム$|\Delta w| > \Delta_ {max}$なら、

$$
\Delta w \leftarrow \Delta w \frac{\Delta_ {max}}{|\Delta w|}
$$

に制限します。注意したいのは勾配の方向が変わらなず、大きさだけ変わります。
これで、１つのパラメータ更新ステップが大きすぎにならないように調整できます。

![image](https://galapagos.qiita.com/files/8765fbc5-fc2f-9e8b-eb8d-2eae242ff286.png)

次は勾配消失問題です。これは主な問題です。実践ではシーケンスの長さが10~20だけで勾配が速く0になります。勾配がなくなると、過去の情報が消えてきます。まるで記憶喪失のようです。勾配消失解決するためにいろんなアプローチがありましたが、その中はLSTMという手法があります。

![image](https://galapagos.qiita.com/files/4891d67d-0d77-f78b-49c0-529ada3facd9.png)

## LSTM (Long Short-Term Memory)
記憶が消える問題に対して、じゃあ、記憶細胞 (memory cell) を入れようという考え方です。
RNNとLSTMをもっと読みたい方はGravesのテキストブック(2012)[^11]を参考してください。

### Memory Cell

![image](https://galapagos.qiita.com/files/4dd9d00a-fec4-f7b1-e159-71f01b2d7673.png)

Memory cellの構造：

* 情報を保存する部分(M)
* Input gate: 情報を書き込むかどうかを制御する(write)
* Output gate: 情報を出力するかどうかを制御する(read)
* Forget gate: 情報を削除かどうかを制御する(forget)


### LSTM Cell

参考資料[^6]の説明がわかりやすいです。
![image](https://galapagos.qiita.com/files/5bfeec6e-5618-c4d9-3ad4-4e6b7c80f2d2.png)

### 感覚的な説明

各ゲートは0から1までの数値を出力する連続関数です。

* Input gate:
    * 0.0のとき、何も書き込まない
    * 1.0のとき、全部書き込む
    * 0.0~1.0の間、一部書き込む
* Output gate:
    * 0.0のとき、何も出力しない
    * 1.0のとき、全部出力する
    * 0.0~1.0の間、一部出力する
* Forget gate:
    * 0.0のとき、全部削除する
    * 1.0のとき、何も削除しない
    * 0.0~1.0の間、一部削除する
* State M (memory): 一番大事な部分です。現在の入力と過去のstateから3つのゲートによって決まります

### 数学的な説明

LSTMはいろいろな異体があります。以下の公式は参考資料[^7]から取ってきます。
実は各ゲートは１つのロジスティックレイヤで、自分がパラメータを持っていて、活性化関数sigmoidとして動きます。

#### 記号
* $h_ t$ : ステップ$t$のステート
* $x_ t$ : ステップ$t$の入力
* $W$ : 現在入力と現在ステートと繋がるパラメータ
* $U$ : 過去ステートと現在ステートと繋がるパラメータ
* $\sigma$ : sigmoid関数
* $\circ$ : 要素ごとの積 (element-wise product)

#### 公式

* Input gate:

$$
i_ t = \sigma\big( W^{(i)}x_ t + U^{(i)}h_ {t-1} \big)
$$

* Forget gate:

$$
f_ t = \sigma\big( W^{(f)}x_ t + U^{(f)}h_ {t-1} \big)
$$

* Output gate:

$$
o_ t = \sigma\big( W^{(o)}x_ t + U^{(o)}h_ {t-1} \big)
$$

* 新しいmemory cell:

$$
\bar{c_ t} = \tanh\big( W^{(c)}x_ t + U^{(c)}h_ {t-1} \big)
$$

* 最終memory cell:

$$
c_ t = f_ t \circ c_ {t-1} + i_ t \circ \bar{c_ t}
$$

* 最終state:

$$
h_ t = o_ t \circ \tanh(c_ t) 
$$

### Memory cellの効果

![image](https://galapagos.qiita.com/files/58b85329-bc0f-23d4-5dc0-c72133d84278.png)

なぜ勾配消失問題を解決できるのか数学的の証明を調べましたが見つけませんでした。まぁ、いいでしょう。
感覚的にはメモリがあるので、特にforget gate = 1にしたら過去の情報を永遠に保存できます。backpropのとき、情報は誤差です。
その結果、勾配消失を避けることができます。

もともとのLSTM (1997年版）はforget gateがなかったです。現在の標準LSTMにforget gateを入れるのはタスクによるけど大昔の情報はあまり要らないので削除してもいいという考え方です。

### 正則化

![image](https://galapagos.qiita.com/files/c1e4b8e9-6761-d2d4-e254-07ee2a304fdd.png)

* L2どこでもOK
* Dropoutは気をつけましょう
    * recurrent部分はダメ
    * 入力・出力部分はOK

詳しくは参考資料[^8]へ！

## Beam Search
![image](https://galapagos.qiita.com/files/7336bc5b-2b31-623b-a55f-3737d2a6526d.png)

RNNを使って次の単語または文字予測することができます。次の出現要素の確立を計算して最大ものを選択します。
同じこと繰り返して、長いテキストを生成できます。

![image](https://galapagos.qiita.com/files/158fe342-01b3-b962-b895-3bbb48754553.png)

但し、生成テキストが長ければ長いほど計算量が急激に大きくなります。これで、Beam searchという手法を使って各ステップで良さそうな文字列だけ残してます。その結果、計算量が大きくならず、良いテキストが生成されます。

Beam searchに関しては参考資料[^9]で遊んでください！

## Captioning and Translation

一般的にRNNは可変長なシーケンス→固定サイズのベクトルを出力できます。Beam searchは固定サイズのベクトル→可変長なシーケンスをマッピングすることができます。それの組み合わせで、Sequence-to-Sequenceモデルができます。Sequence-to-Sequenceの説明はTensorflowのチュートリアルが良いです[^10]。

![image](https://galapagos.qiita.com/files/51156540-fb91-c8f5-3f6c-7bbd8673be3d.png)

### sequence-to-sequenceの応用：

* 機械翻訳：
    * 入力：英語シーケンス
    * 出力：日本語シーケンス
* 音声認識：
    * 入力：音声
    * 出力：テキスト
* 画像キャプションニング
    * 入力：画像
    * 出力：テキスト

[^1]: 共有重みについて https://galapagos.qiita.com/vanhuyz/items/82e321481dece891338f#statistical-invariance
[^2]: Pascanu et al (2012). On the difficulty of training Recurrent Neural Networks http://arxiv.org/abs/1211.5063
[^3]: StandfordのRNN講義資料 https://cs224d.stanford.edu/lectures/CS224d-Lecture8.pdf
[^4]: 実は固有値の最大値（スペクトル半径）だけ考慮すれば良い
[^5]: 行列のn乗の計算方法 http://math.stackexchange.com/questions/735532/how-to-compute-the-n-textrmth-power-of-a-matrix
[^6]: Understanding LSTM Networks http://colah.github.io/posts/2015-08-Understanding-LSTMs/ 日本語訳 http://qiita.com/KojiOhki/items/89cd7b69a8a6239d67ca
[^7]: StandfordのLSTM講義資料 https://cs224d.stanford.edu/lectures/CS224d-Lecture9.pdf
[^8]: Wojciech Zaremba et al (2014). Recurrent Neural Network Regularization http://arxiv.org/abs/1409.2329
[^9]: http://spheresofa.net/blog/?p=1044
[^10]: Tensorflow Sequence-to-Sequenceチュートリアル https://www.tensorflow.org/versions/r0.9/tutorials/seq2seq/index.html
[^11]: Graves (2012). Supervised Sequence Labelling with Recurrent Neural Networks http://www.cs.toronto.edu/~graves/preprint.pdf

