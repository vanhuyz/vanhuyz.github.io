+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-04-29T08:41:59Z
description = ""
draft = false
slug = "prml-chap2-probability-distributions"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 2: Probability Distributions"

+++


## 2.3.3 Bayes' theorem for Gaussian variables

### Tóm lại
Nếu $p(x)$ và $p(y|x)$ đều là các phân phối chuẩn thì $p(y)$ và $p(x|y)$ cũng là các phân phối chuẩn.

---

### Công thức:
Giả sử

$$
\begin{align}
p(x) & = \mathcal{N}(x|\mathbf{\mu}, \mathbf{\Lambda}
^{-1}\big) \\\
p(y|x)& = \mathcal{N}\big(y|\mathbf{A}x+\mathbf{b}, \mathbf{L}
^{-1}\big) \\\
\end{align}
$$

thì 

$$
\begin{align}
p(y) & = &\mathcal{N}\big(y|\mathbf{A}\mu+b,\mathbf{L}^{-1}+\mathbf{A}\mathbf{\Lambda}^{-1}\mathbf{A}^{\mathrm{T}}\big)\\\
p(x|y) & = &\mathcal{N}\big(x|\Sigma\{ \mathbf{A}^{\mathrm{T}}\mathbf{L}(y-b)+\mathbf{\Lambda}\mu\},\Sigma\big)
\end{align}
$$

với

$$
\Sigma = (\mathbf{\Lambda}+\mathbf{A}^{T}\mathbf{L}\mathbf{A})^{-1}
$$

---
## 2.3.4 Maximum likelihood for the Gaussian

Data set 

$$
X = (x_ 1,x_ 2,\ldots,x_ N)^{\mathrm{T}}
$$

Giả sử data này theo Gaussian thì dùng Maximum likelihood ta có thể tính được mean và covariance như sau:

$$
\begin{align}
\mu_ {ML} &= \frac{1}{N}\sum_ {n=1}^N x_ n \\\
\Sigma_ {ML} &= \frac{1}{N}\sum_ {n=1}^N(x_ n-\mu_ {ML})(x_ n-\mu_ {ML})^{\mathrm{T}}
\end{align}
$$

---
Nếu gọi $\mu$ và $\Sigma$ là *true* mean, *true* covariance của phân phối ẩn dưới dữ liệu.
Xét kỳ vọng của các giá trị tìm được 

$$
\begin{align}
\mathop{\mathbb{E}}[\mu_ {ML}] &= \mu \\\
\mathop{\mathbb{E}}[\Sigma_ {ML}] &= \frac{N-1}{N}\Sigma
\end{align}
$$

$\mu_ {ML}$ thì OK nhưng $\Sigma_ {ML}$ thì bị sai số. Cần hiệu chỉnh lại như sau:

$$
\tilde\Sigma = \frac{1}{N-1}\sum_ {n=1}^N(x_ n-\mu_ {ML})(x_ n-\mu_ {ML})^{\mathrm{T}}
$$

* *Note* (ngoài sách): thực ra khi N rất lớn thì cái sai số nó chẳng đáng bao nhiêu nên dùng thế nào cũng được.

---

## 2.3.5 Sequential estimation

* 1 kiểu online learning
    * nghĩa là ko phải dùng tất cả dữ liệu cùng 1 lúc (batch processing) mà dùng từng dữ liệu 1 để thay đổi parameter dần dần

    * dùng trong trường hợp batch processing quá nặng (e.g: data nhiều quá không cho hết vào memory được )
    * tất cả data không có trong tay hết mà "đến từ từ" (kiểu các ứng dụng online) 

---

### Ví dụ trong Maximum Likelihood for Gaussian

$$
\begin{align}
\mu_ {ML}^{(N)} &= \frac{1}{N}\sum_ {n=1}^N x_ n \\\
&=\frac{1}{N}x_ N+\frac{1}{N}\sum_ {n=1}^{N-1}x_ n \\\
&=\frac{1}{N}x_ N + \frac{N-1}{N}\mu_ {ML}^{(N-1)} \\\
&=\mu_ {ML}^{(N-1)}+\frac{1}{N}\big(x_ N-\mu_ {ML}^{(N-1)}\big)
\end{align}
$$ 

Như vậy, ta thấy có thể tính $\mu_ {ML}^{(N)}$ dựa vào $\mu_ {ML}^{(N-1)}$ với dữ liệu $x_ N$.

---
### Tổng quát
* Dùng thuật toán Robbins-Monro
* Chẳng hạn dùng Maximum likelihood để tìm parameter $\theta$:

$$
\theta^{(N)} = \theta^{(N-1)}-a_ {N-1}\frac{\partial}{\partial \theta^{(N-1)}}\big[ \ln p(x_ N|\theta^{(N-1)} )\big]
$$
với $\\{a_ N\\}$ là dãy hệ số dương thoả mãn 3 điều kiện:

$$
\lim_ {N \to \infty} = 0 \\\
\sum_ {N=1}^{\infty} a_ N = \infty \\\
\sum_ {N=1}^{\infty} a_ N^2 < \infty
$$

Đối với từng bài toán cụ thể thì chọn $\\{a_ N\\}$ cho hợp lý.

---

## 2.3.6 Bayesian inference for the Gaussian
Giả sử có data $\mathbf{X} = \\\{x_ 1,\ldots,x_ N\\\}$ với variance $\sigma^2$ đã biết trước. Mục tiêu là tìm mean $\mu$.

Likelihood:

$$
\begin{align}
p(\mathbf{\mathbf{X}|\mu})& = \prod_ {n=1}^{N}p(x_ n|\mu) \\\
&=\frac{1}{(2\pi\sigma^2)^{N/2}}\exp{\big\\\{ -\frac{1}{2\sigma^2} \sum_ {n=1}^{N}(x_ n-\mu)^2 \big\\\}}
\end{align}
$$

Giả sử prior là 1 Gaussian

$$
p(\mu) = \mathcal{N}\big(\mu|\mu_ 0, \sigma_ o^2\big)
$$

thì posterior sẽ là 1 Gaussian

$$
p(\mu|\mathbf{X}) = \mathcal{N}\big(\mu|\mu_ N, \sigma_ N^2\big)
$$

với

$$
\begin{align}
\mu_ N &= \frac{\sigma^2}{N\sigma_ 0^2+\sigma^2}\mu_ 0+\frac{N\sigma_ 0^2}{N\sigma_ 0^2+\sigma^2}\mu_ {ML} \\\
\frac{1}{\sigma_ N^2} &=\frac{1}{\sigma_ 0^2}+ \frac{N}{\sigma^2}
\end{align}
$$

$\mu_ {ML}$ là cái mean đã tính dùng Maximum likelihood.

Nhìn công thức ta thấy là khi N nhỏ thì $\mu_ N$ sẽ xấp xỉ $\mu_ 0$, N lớn dần thì $\mu_ N$ sẽ càng ngày càng tiệm cận dần $\mu_ {ML}$.
Đối với precision (nghịch đảo của variance) thì cũng tương tự.

---

### Nhìn theo hướng Sequential estimation

$$
p(\mu|\mathbf{X}) \sim \Big[ p(\mu) \prod_ {n=1}^{N-1}p(x_ n|\mu) \Big] p(x_ N|\mu)
$$

Cái trong ngoặc vuông chính là posterior của N-1 data đầu, nhưng có thể nhìn là prior cho N data, với likelihood gắn với $x_ N$.

---

### Đối với bài toán 
biết trước $\mu$ và mục đích là đi tìm precision $\lambda = 1/\sigma^2$

Posterior trong trường hợp này có họ với phân phối Gamma, tính lòng vòng 1 lúc thì cũng ra được kết quả.

Kết quả cũng không có gì thú vị lắm nhưng nếu anh em nào quan tâm thì đọc thêm trong sách :sweat_ smile:

