+++
author = "Huy Van"
categories = ["TensorFlow", "Japanese"]
date = 2017-01-16T06:28:00Z
description = ""
draft = false
slug = "install-tensorflow-with-anaconda"
tags = ["TensorFlow", "Japanese"]
title = "AnacondaによるTensorFlowインストール手順"

+++


Anacondaによるインストールは大変楽という噂がありますので試してみました。
AnacondaはデフォルトNumPy、 Pandas、 SciPy、 Matplotlib、 Jupyterなどが入っています。
他のパッケージ、例えばTensorFlow、 OpenCVも簡単にインストールでき、pyenv/virtualenvがいらなくなるのでおすすめです。

* 環境: Ubuntu 16.04

## Anacondaインストール
* スクリプトダウンロード
URL変える可能性があるのでまずHP確認してください。 https://www.continuum.io/downloads#linux

```bash
$ wget https://repo.continuum.io/archive/Anaconda3-4.2.0-Linux-x86_64.sh
```

* スクリプト実行

```bash
$ bash Anaconda3-4.2.0-Linux-x86_64.sh
$ source ~/.bashrc
```

## TensorFlowインストール
詳細はこちら [https://www.tensorflow.org/get_started/os_setup#anaconda_installation]()

```bash
$ conda create -n tensorflow python=3.5
```

condaコマンドでインストールできるのは現在CPUバージョンだけです。GPUインストールしたい場合はpipでインストールしてください。

```bash
$ source activate tensorflow
$ conda install -c conda-forge tensorflow
```

確認：

```bash
$ python
Python 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:53:06)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-1)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow as tf
>>> tf.__version__
'0.12.1'
```

完了！

