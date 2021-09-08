+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-04-29T08:58:59Z
description = ""
draft = false
slug = "prml-chap3-linear-models-for-regression"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 3: Linear Models for Regression"

+++


## The Evidence Approximation

Trong Bayesian đối với linear model, ta đã thấy sự xuất hiện của $\alpha$ và $\beta$ là các hyperparameters của prior và noise.

Trong chương này, ta sẽ cố gắng tìm các giá trị này dựa trên maximizing the *magrinal likelihood function*.

Framework này được gọi là **evidence approximation**.

Theo công thức Bayes:

$$
p(\alpha, \beta|\mathbf{t}) \propto p(\mathbf{t}|\alpha,\beta)p(\alpha,\beta)
$$

$p(\mathbf{t}|\alpha,\beta)$ là marginal likelihood function

---

### Evaluation of the evidence function

Marginal likelihood function có thể triển khai theo $\mathbf{w}$ như sau:

$$
p(\mathbf{t}|\alpha,\beta) = \int p(\mathbf{t}|\mathbf{w},\beta)p(\mathbf{w}|\alpha) \mathrm{d}\mathbf{w}
$$

Khai triển loằng ngoằng thì cuối cùng tính được log của cái marginal likelihood như sau:

$$
\ln p(\mathbf{t}|\alpha,\beta) = \frac{M}{2}\ln \alpha + \frac{N}{2}\ln \beta -E(\mathbf{m}_ N) -\frac{1}{2}\ln|\mathbf{A}| -\frac{N}{2}\ln(2\pi)
$$

Ở đây N là data size, M là số parameters trong linear model. 
$\mathbf{A}$ là cái ma trận Hessian của error function $E(\mathbf{w})$.
$\mathbf{m}_ N$ thì được tính bằng cái công thức (3.84).

---
### Maximizing the evidence function

Tìm được cái công thức tính log của marginal likelihood rồi giờ tìm $\alpha, \beta$ để max cái đó.

Đơn giản chỉ việc tính đạo hàm theo $\alpha$ rồi $\beta$ là được.

Cuối cùng sẽ tính được $\alpha$ như sau:

$$
\alpha = \frac{\gamma}{\mathbf{m}_ N^{T}\mathbf{m}_ N}
$$

$\gamma$ này được tính từ các giá trị riêng của cái Hessian $\mathbf{A}$.

$\beta$ thì cũng tính được như sau:

$$
\frac{1}{\beta} = \frac{1}{N-\gamma} \sum_ {n=1}^{N} \big\\{t_ n-\mathbf{m}_ N^T\phi(x_ n)\big\\}^2
$$

Chú ý là $\alpha, \beta$ không tính trực tiếp được mà sẽ tính bằng cách chọn giá trị ban đầu bất kỳ rồi tính $\mathbf{m}_ N, \gamma$ rồi update $\alpha, \beta$ cho đến khi hội tụ thì dừng.

---

### Effective number of parameters

Trường hợp đặc biệt nếu $N >> M$ thì $\gamma = M$, $\alpha, \beta$ có thể tính được như sau:

$$
\alpha = \frac{M}{2E_ W(\mathbf{m}_ N)} \\\
\beta = \frac{N}{2E_ D(\mathbf{m}_ N)}
$$

với 

$$
E_ W(\mathbf{m}_ N) = \frac{1}{2} \mathbf{m}_ N^T\mathbf{m}_ N \\\
E_ D(\mathbf{m}_ N) = \frac{1}{2} \sum_ {n=1}^N \{t_ n-\mathbf{m}_ N^T\phi(x_ n)\}^2
$$

$\alpha, \beta$ có thể tính được mà không cần phụ thuộc vào cái Hessian.

---

## Limitations of Fixed Basis Functions

* Vì chọn basis function $\phi_ j(x)$ cố định trước khi quan sát training dât nên có thể bị dính vào curse of dimensionality.
* May mắn là dữ liệu thực tế thường có những tính chất đặc biệt nên có thể áp dụng các ràng buộc đặc biệt mà chúng ta sẽ học trong các chương sau.

