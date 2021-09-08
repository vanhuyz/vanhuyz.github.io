+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T11:43:53Z
description = ""
draft = true
slug = "udacity-deeplearning-assignment-1"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - Assignment 1: notMNIST - Problem 4,5,6"

+++


# About
* Deep Learning勉強会のAssignment 1の続き
* ソースコード：https://github.com/vanhuyz/udacity-dl/blob/master/1_notmnist.ipynb

## Problem 4

> Convince yourself that the data is still good after shuffling!
> データをシャッフルした後問題ないのかを確認

各データセットの１番目の画像を表示し、ラベルと合っているのかを確認します。
注意したいのはラベルは0~9の数字で、これは文字A~Jと相当します。

まずはtrain_dataset:

```python
plt.title(train_labels[0])
plt.imshow(train_dataset[0],cmap='Greys_r')
```

![image](https://galapagos.qiita.com/files/6164fdb8-e086-5ed6-97ef-f9bca09660b4.png)

ラベル1、つまり文字Bなので合っていますね！

同様に、valid_setとtest_set:

```python
plt.title(test_labels[0])
plt.imshow(test_dataset[0],cmap='Greys_r')
```


![image](https://galapagos.qiita.com/files/a2d58eba-90e2-2038-bf52-6bf5deaa154d.png)

```python
plt.title(test_labels[0])
plt.imshow(test_dataset[0],cmap='Greys_r')
```

![image](https://galapagos.qiita.com/files/4ee6724c-b43e-fbd8-6c7c-8cc88cf77b8d.png)

## Problem 5

> 各データセット間の重複度どのくらいかを確認する

アイデアは小さい方のセットをloopで回して、大きい方のセットにその要素があるかをカウントします。

[np.equal](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.equal.html)を使いたいのでちょっと説明します。

例えば、Aは1d配列、cはInteger：

```pycon
>>> A = np.array([1,2,3])
>>> A
array([1, 2, 3])
>>> c = 1
>>> np.equal(A,c)
array([ True, False, False], dtype=bool)
```

Aの各要素がcに対して比較になります。
cがAに含まれるのかを確認したいなら[any](http://docs.scipy.org/doc/numpy-1.10.0/reference/generated/numpy.ndarray.any.html)を使えばいいです。

```pycon
>>> np.equal(A,c).any()
True
>>> d = 4
>>> np.equal(A,d)
array([False, False, False], dtype=bool)
>>> np.equal(A,d).any()
False
```

これは1d配列。2d配列の場合はどうなりますかというと：

```pycon
>>> A = np.array([[1,2,3],[1,3,4]])
>>> A
array([[1, 2, 3],
       [1, 3, 4]])
>>> c = np.array([1,3,4])
>>> c
array([1, 3, 4])
>>> np.equal(A,c)
array([[ True, False, False],
       [ True,  True,  True]], dtype=bool)
```

cがAに含まれるなら`[ True,  True,  True]`があるということになりますね。
それで、[all](http://docs.scipy.org/doc/numpy-1.10.0/reference/generated/numpy.ndarray.all.html)を使って、最後`any`をかけばいいです。

```pycon
>>> np.equal(A,c).all(1)
array([False,  True], dtype=bool)
>>> np.equal(A,c).all(1).any()
True
```

`all(1)`は`axis=1`(つまり行列の行）を指定します。

同様に、3d配列は`all(1)`を2回使えばいいです。本当に馬鹿みたいですが、動けばいいのです！

最終のコード：

```python
def duplicates_count(A, B):
  count = 0
  print("Progress(%):", end=" ")
  for idx,c in enumerate(B):
    # 進捗出力（死んでいないかの確認）
    if idx % 100 == 0:
      print(int(idx/100), end=" ")
    if np.equal(A,c).all(1).all(1).any():
      count += 1

  return count

duplicates_count(train_dataset,valid_dataset)
```

実行結果：

```pycon
>>> duplicates_count(valid_dataset,test_dataset)
203
>>> duplicates_count(train_dataset,valid_dataset)
## 30分ぐらいかかる
1066
```


> Optional questions:
- What about near duplicates between datasets? (images that are almost identical)

`np.equal`の代わりに[np.isclose](http://docs.scipy.org/doc/numpy-1.10.0/reference/generated/numpy.isclose.html)が良さそうです。

> - Create a sanitized validation and test set, and compare your accuracy on those in subsequent assignments.

`count += 1`の代わりにその要素をdeleteすればよいです。


## Problem 6

> Train a simple model on this data using 50, 100, 1000 and 5000 training samples. Hint: you can use the LogisticRegression model from sklearn.linear_model.

【参考】[sklearnのLogisticRegressionドキュメント](
http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)

動けばいいのでパラメータを全然チューニングしないで実装します。

```python
train_num = 5000

## 画像の28x28の行列をでかいベクトルに変換
X_train = train_dataset[:train_num].reshape(train_num,28*28)
y_train = train_labels[:train_num]

## sklearn.linear_modelからのLogisticRegressionモデルをロード
logreg = LogisticRegression()

## 学習
logreg.fit(X_train,y_train)

## validation setで評価。とりあえず先頭1000個だけで！
valid_num = 1000
X_valid = valid_dataset[:valid_num].reshape(valid_num,28*28)
y_valid = valid_labels[:valid_num]

## accuracy rate
acc = sum(logreg.predict(X_valid) == y_valid)/valid_num
print("Accuracy rate: %f" % acc)
```

結果：

```
Accuracy rate: 0.758000
```

> Optional question: train an off-the-shelf model on all the data!

```python
X_train = train_dataset.reshape(-1,28*28)
y_train = train_labels

logreg = LogisticRegression()
logreg.fit(X_train,y_train)

X_valid = valid_dataset.reshape(-1,28*28)
y_valid = valid_labels
X_test = test_dataset.reshape(-1,28*28)
y_test = test_labels

valid_acc = sum(logreg.predict(X_valid) == y_valid)/len(X_valid)
print("Accuracy rate on validation set: %f" % valid_acc)

test_acc = sum(logreg.predict(X_test) == y_test)/len(X_test)
print("Accuracy rate on test set: %f" % test_acc)
```

40分待つと、

```
Accuracy rate on validation set: 0.823700
Accuracy rate on test set: 0.894000
```

なぜかvalidation setよりtest setの方がずっと高いでしょう！

感想：
sklearnのLogistic Regressionの実装は楽ちん！
ライブラリを利用せず実装したい方は [機械学習プログラミング課題２解答例](https://vanhuyz.com/coursera-machinelearning-assignment-2/) を参考してください！（懐かしい）

