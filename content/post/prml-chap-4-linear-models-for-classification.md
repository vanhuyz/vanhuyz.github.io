+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-04-29T09:13:03Z
description = ""
draft = false
slug = "prml-chap-4-linear-models-for-classification"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 4: Linear Models for Classification"

+++


## 4.3.1 Fixed basis functions

* Bài toán là phân loại các input vector x
* Áp 1 hàm phi tuyến cố định $\phi(x)$ vào thì bài toán trở thành phân loại các $\phi(x)$ <br> → đường decision boundary sẽ trờ thành tuyến tính 
<br> → bài toán trở nên đơn giản đi rất nhiều 
<img width="964" alt="Screen Shot 2017-04-16 at 13.51.43.png" src="https://galapagos.qiita.com/files/b4decfad-1fa1-ea8d-dfab-b717932ab559.png">

---
## 4.3.2 Logistic regression
* Thuật toán này rất quan trọng nên anh em cần chú ý hiểu kỹ
* Tên là regression nhưng lại là thuật toán classification
* Trở lại bài toán phân biệt 2 lớp: data set $\\{\phi_ n, t_ n\\}$ với $t_ n \in \\{0,1\\}$ và $n=1,\ldots,N$
* Hàm likelihood trở thành

$$
p(\mathbf{t}|w) = \prod_ {n=1}^N y_ n^{t_ n}\{1-y_ n\}^{1-t_ n}
$$
với $\mathbf{t} = (t_ 1,\ldots,t_ N)^T$ và $y_ n = p(C_ 1 | \phi_ n)=y(\phi) = \sigma(w^T\phi)$ là xác suất để $\phi_ n$ rơi vào lớp $C_ 1$.

---
* Maximum likelihood sẽ trở thành minimize *negative log likelihood*. Trong trường hợp này hàm này còn gọi là **cross entropy**

$$
E(w) = -\ln p(\mathbf{t}|w) = -\sum_ {n=1}^N \{t_ n\ln y_ n + (1-t_ n)\ln (1-y_ n) \}
$$

 Tính gradient:

$$
\nabla E(w)= \sum_ {n=1}^N(y_ n-t_ n)\phi_ n
$$
Kỳ diệu ở chỗ cái gradient này giống hệt với gradient trong của hàm sum-of-squares error cho linear regression model.

---
## 4.3.3 Iterative reweighted least squares

Vì sigmoid là hàm phi tuyến nên không giải ra được nghiệm chính xác như của linear regression đươc. Trong chương này Bishop dạy chúng ta cách update weight theo kỹ thuật  $Newton-Raphson$ như sau:

$$
w^{(new)} = w^{(old)} - \mathbf{H}^{-1}\nabla E(w)
$$

với $\mathbf{H} = \nabla\nabla E(w)$ là ma trận Hessian.

※ Ngoài luồng: so sánh với Gradient Descent thì cái này không cần learning rate $\alpha$ → đỡ được hyper parameter cũng tốt.

---
## 4.3.4 Multiclass logistic regression

* Tóm lại 1 câu: thay vì sigmoid thì dùng hàm softmax, còn lại các công thức thì giống hệt so với bài toán 2 lớp

$$
p(C_ k|\phi) = y_ k(\phi) = \textrm{softmax}(a_ k) = \frac{\exp(a_ k)}{\sum_ j \exp(a_ j)}
$$

với $a_ k = w_ k^T\phi$

---
## 4.3.5 Probit regression

* probit = probability unit
* Trở lại với bài toán 2 lớp

$$
p(t=1|a) = f (a)
$$

với $a=w^T\phi$ và $f(.)$ là activation function

Chọn 1 threshold $\theta$ thì giả sử ta xét hàm như sau:

$$
\left\\{
\begin{array}{ll}
t_ n = 1 & (\textrm{if} \\: a_ n\geq 0) \\\
t_ n = 0 & \textrm{(otherwise)}
\end{array}
\right.
$$

$\theta$　lấy từ phân phối $p(\theta)$ thì có thể chọn activation $f(a)$ như sau:

$$
f(a) = \int_ {-\infty}^a p(\theta) \mathrm{d}\theta
$$

Cái này chính là cumulative distribution function của $p(\theta)$.

---
## Ví dụ

<img width="932" alt="Screen Shot 2017-04-16 at 15.29.12.png" src="https://galapagos.qiita.com/files/34b65ebf-1cd2-7536-eed2-b84947ac2111.png">

Ví dụ $p(\theta)$ là 1 Gaussian với mean=0, variance=1. Cumulative distribution function sẽ trở thành:

$$
\Phi(a) = \int_ {-\infty}^a \mathcal{N}(\theta|0,1) \mathrm{d}\theta
$$

hàm này được gọi là *probit* function. Hàm này có hình chữ S giống hàm sigmoid.

