+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-02-18T15:56:40Z
description = ""
draft = false
slug = "prml-chap1-probability-theory"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 1: Probability Theory"

+++


## Ví dụ

Có 2 hộp: Đỏ, Lam
Có 2 loại quả: Táo(màu lá), Cam(màu cam)

<img width="438" alt="figure 1.9.png" src="https://galapagos.qiita.com/files/fc15481c-83cc-96b4-944a-55d2b24aae32.png">

Chọn 1 hộp bất kỳ rồi bốc 1 quả bất kỳ trong hộp đó

<img width="438" alt="figure 1.9.png" src="https://galapagos.qiita.com/files/fc15481c-83cc-96b4-944a-55d2b24aae32.png">

---

## Biến ngẫu nhiên (Random variable)
`B`: hộp, có thể nhận 1 trong 2 giá trị `r`(đỏ), `b` (lam)
`F`: quả, có thể nhận 1 trong 2 giá trị `a`(táo), `o` (cam)

<img width="438" alt="figure 1.9.png" src="https://galapagos.qiita.com/files/fc15481c-83cc-96b4-944a-55d2b24aae32.png">

---

## Ký hiệu xác suất

Giả sử xác xuất chọn hộp đỏ trong 2 hộp là `4/10`

$$ 
p(B=r) = 4/10 \\\\
p(B=b) = 6/10
$$

---
## Conditional probability

p(Y|X): the probability of Y given X 

Ví dụ: Biết rằng ta đã bốc được hộp đỏ. Hỏi xác suất bốc được quả táo trong đó là bao nhiêu?

$$
p(F=a|B=r) = 2/8
$$

<img width="438" alt="figure 1.9.png" src="https://galapagos.qiita.com/files/fc15481c-83cc-96b4-944a-55d2b24aae32.png">

---
## Joint probability
p(X,Y): the probability of X *and* Y

Là xác suất xảy ra cả 2 sự kiện cùng 1 lúc
Ví dụ: xác suất chọn quả táo và quả táo đó ở hộp đỏ là bao nhiêu?

$$
\begin{align}
p(B=r,F=a) &=  p(F=a|B=r) \times p(B=r) \\\
&= \frac{2}{8} \times \frac{4}{10} \\\
&= \frac{1}{10}
\end{align}
$$

<img width="438" alt="figure 1.9.png" src="https://galapagos.qiita.com/files/fc15481c-83cc-96b4-944a-55d2b24aae32.png">

---
## Marginal probability

$$
\begin{align}
p(B=r) & = p(B=r,F=a) + p(B=r,F=o) \\\
& = \frac{1}{10} + \frac{3}{10}
& = \frac{4}{10}
\end{align}
$$

---
## The Rules of Probability
X,Y: random variables
Sum rule:

$$
p(X) = \sum_Y p(X,Y)
$$

Product rule:

$$
p(X,Y) = p(Y|X)p(X)
$$

---
## Chú ý

Tổng tất cả các xác suất phải bằng 1

$$
\sum_X p(X)= 1
$$

---
## Bayes' theorem

$$
p(Y|X) = \frac{p(X|Y)p(Y)}{p(X)}
$$

`p(Y)`: prior probability
`P(Y|X)`: posterior probability

---
<img width="957" alt="fig 1.11.png" src="https://galapagos.qiita.com/files/11d8a9c9-4fc9-9b77-8cc4-fceac575d587.png">

---
## Probability densities
* Xác suất trong khoảng $(x, x+\delta x$) với $\delta x \rightarrow 0$ là $p(x)\delta x$.
* Xác suất trong khoảng $(a, b$) là

$$
p(x\in (a,b)) = \int_a^b p(x)\mathrm{d}x
$$

---
<img width="748" alt="fig 1.12.png" src="https://galapagos.qiita.com/files/8834b972-c198-d4c3-9f49-8af930a0e638.png">

---

## Tính chất

$$
\begin{align}
p(x) & \ge 0 \\\
\int_{-\infty}^{\infty} p(x)\mathrm{d}x & = 1
\end{align}
$$

---

## Cumulative distribution function

Xác suất để x nằm trong khoảng $(-\infty,z)$:

$$
P(z) = \int_{-\infty}^{z} p(x)\mathrm{d}x
$$

---
## Sum and product rules

$$
\begin{align}
p(x) &= \int p(x,y)\mathrm{d}y \\\
p(x,y) &= p(y|x)p(x)
\end{align}
$$

---
## Expectations (Kỳ vọng)
Giá trị kỳ vọng của hàm $f(x)$

* Biến rời rạc

$$
\mathbb{E}[f] = \sum_x p(x)f(x)
$$

* Biến liên tục

$$
\mathbb{E}[f] = \int_x p(x)f(x)\mathrm{d}x
$$

---
## Mở rộng

* Kỳ vọng hàm nhiều biến: cần chỉ rõ là kỳ vọng theo biến nào

$$
\mathbb{E}_x[f(x,y)]
$$

* Conditional expectation

$$
\mathbb{E}_ x[f|y] = \sum_x p(x|y)f(x)
$$

---
## Variance (Phương sai)

$$
\begin{align}
\mathrm{var}[f] & = \mathbb{E}\big[\big(f(x) - \mathbb{E}[f(x)]\big)^2\big] \\\
& = \mathbb{E}[f(x)^2] - \mathbb{E}[f(x)]^2
\end{align}
$$

* Đặc biệt:

$$
\mathrm{var}[x] = \mathbb{E}[x^2] - \mathbb{E}[x]^2
$$

---
## Covariance 
Covariance giữa 2 biến x,y là 1 ma trận:

$$
\begin{align}
\mathrm{cov}[x,y] &= \mathbb{E}_ {x,y}\big[\{x-\mathbb{E}[x]\}\{y-\mathbb{E}[y]\}\big] \\\
&= \mathbb{E}_ {x,y}[xy] -\mathbb{E}[x]\mathbb{E}[y]
\end{align}
$$

Trường hợp biến ngẫu nhiên nhiều chiều:

$$
\begin{align}
\mathrm{cov}[\mathbf{x},\mathbf{y}] &= \mathbb{E}_ {\mathbf{x},\mathbf{y}}\big[\{\mathbf{x}-\mathbb{E}[\mathbf{x}]\}\{\mathbf{y}^T-\mathbb{E}[\mathbf{y}^T]\}\big] \\\
&= \mathbb{E}_ {\mathbf{x},\mathbf{y}}[\mathbf{x}\mathbf{y}^T] -\mathbb{E}[\mathbf{x}]\mathbb{E}[\mathbf{y}^T]
\end{align}
$$

---
## Baysian probabilities

Quay trở lại với bài toán curve fitting.

* Observed data $ \mathcal{D} = {t_ 1,\ldots,t_ N} $
* Parameters $\mathbf{w}$  

---

### Frequentist perspective

* $\mathbf{w}$ là cố định (chỉ tồn tại 1)
* Likelihood: $p(\mathcal{D}|\mathbf{w})$
    * có parameters $\mathbf{w}$, xác suất để sinh ra data $\mathcal{D}$
* Maximum likelihood:
    * tìm $\mathbf{w}$ để $p(\mathcal{D}|\mathbf{w})$ là lớn nhất

---
### Bayes perspective

$\mathbf{w}$ không cố định nên cũng có thể coi là 1 biến ngẫu nhiên với xác suất là $p(\mathbf{w})$

Theo định lý Bayes:

$$
p(\mathbf{w}|\mathcal{D}) = \frac{p(\mathcal{D}|\mathbf{w})p(\mathbf{w})}{p(\mathcal{D})}
$$

Thay vì chỉ tìm max của likelihood thì cần phải tìm max của posterior $p(\mathbf{w}|\mathcal{D})$

