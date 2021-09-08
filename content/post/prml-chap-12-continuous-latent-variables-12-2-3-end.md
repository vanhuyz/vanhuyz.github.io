+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-10-15T06:41:00Z
description = ""
draft = false
slug = "prml-chap-12-continuous-latent-variables-12-2-3-end"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 12: Continuous Latent Variables - 12.2.3 ~ end"

+++


## 12.2.3 Bayesian PCA
Graphical model cho Bayesian PCA được biểu diễn như sau:
![](/content/images/2017/10/f12.13.png)

Ở đây ta đã đưa thêm Gaussian prior độc lập cho $\mathbf{W}$ với các precision $\alpha_i$:

![](/content/images/2017/10/eq12.60.png)

Giá trị của $\alpha_i$ được tìm bằng cách maximizing the maginal likelihood:

![](/content/images/2017/10/eq12.61.png)

Chú ý là để đơn giản ở đây ta coi $\mathbf{\mu}$ và $\sigma^2$ là các parameters chứ không đưa thêm priors cho chúng.

Sử dụng Laplace approximation, các $\alpha_i$ sẽ tìm được như sau:

![](/content/images/2017/10/eq12.62.png)

như theo công thức (3.98). Ở đây $D$ là số chiều của $\mathbf{w}_i$.

Còn lại thì sử dụng EM hoàn toàn tương tự như phần 12.2.2.

## 12.2.4 Factor analysis
Factor analysis là 1 linear-Gaussian latent variable model rất gần với probabilistic PCA, chỉ khác ở chỗ conditional distribution của observed variable $\mathbf{x}$ cho bởi latent variable $\mathbf{z}$ có covariance dạng diagonal thay vì isotropic:

![](/content/images/2017/10/eq12.64.png)

với $\Psi$ là $D \times D$ diogonal matrix.

Sử dụng (2.115), ta tính được marginal distribution cho observed variable $p(x)=\mathcal{N}(x|\mathbf{\mu},\mathbf{C})$ với $\mathbf{C}=\mathbf{W}\mathbf{W}^T + \mathbf{\Psi}$.

Tìm parameters $\mathbf{\mu}$,$\mathbf{W}$ và $\mathbf{\Psi}$ bằng maximum likelihood. Solution $\mathbf{\mu}$ sẽ là sample mean. Tuy nhiên ta không tìm được closed-form solution cho $\mathbf{W}$. Nhưng có thể tính bằng EM. Cụ thể, E-step:

![](/content/images/2017/10/eq12.66.png)

M-step:

![](/content/images/2017/10/eq12.69.png)

# 12.3 Kernel PCA
Ở chương 6 ta đã nhìn thấy kỹ thuật thay thế kernel cho scalar product $x^Tx'$. Giờ tìm cách làm tương tự cho PCA.

Có data set $\\{x_ n \\}$ với $n=1,\ldots,N$ trong không gian có số chiều là $D$. Để đơn giản ta giả sử ta đã trừ các vector $x_ n$ cho sample mean để được $\sum_ n x_ n = 0$. 
Nhơ lại là principal components được định nghĩa bởi vector riêng $\mathbf{u}_ i$ của covariance matrix:

![](/content/images/2017/10/eq12.71-1.png)

và các vector riêng được normalized để $\mathbf{u}_ i^T \mathbf{u}_ i = 1$.

Giờ coi 1 phép biến đổi phi tuyến $\phi(x)$ tới không gian M chiều, sao cho mỗi data point $x_ i$ được chiếu thành $\phi(x_ n)$.

![](/content/images/2017/10/f12.16.png)

Giả sử là data sau khi chiếu cũng có mean là 0 tức là $\sum_ n \phi(x_ n) = 0$. $M \times M$ sample covariance matrix sẽ là:

![](/content/images/2017/10/eq12.73.png)

và vector riêng của nó được định nghĩa bởi:

![](/content/images/2017/10/eq12.74.png)

với $i=1,\ldots,M$. Mục tiêu của chúng ta là tìm giá trị riêng này. Từ định nghĩa của $\mathbf{C}$, $v_ i$ phải thoả mãn:

![](/content/images/2017/10/eq12.75.png)

Thay vào phương trình vector riêng ta được:

![](/content/images/2017/10/eq12.77.png)

Bước chính là biểu diễn cái trên thành kernel function $k(x_ n, x_ m) = \phi(x_ n)^T\phi(x_ m)$ bằng cachs nhân 2 vế với $\phi(x_ l)^T$ để được: 

![](/content/images/2017/10/eq12.78.png)

Viết lại bằng ký hiệu matrix:
![](/content/images/2017/10/eq12.79.png)

Bỏ $\mathbf{K}$ ở cả 2 vế:
![](/content/images/2017/10/eq12.80.png)

Điều kiện normalization cho hệ số $\mathbf{a}_ i$ yêu cầu là vector riêng cũng phải được normalized. Sử dụng (12.76) và (12.80) ta đươc:
![](/content/images/2017/10/eq12.81.png)

Sử dụng (12.76) hình chiếu của điểm $x$ trên vector riêng $i$ là:

![](/content/images/2017/10/eq12.82.png)

Cái này cũng được biểu diễn theo kernel function.

---

Trên đây ta đã biểu diễn được projected data set cho bởi $\phi(x_ n)$ có zero mean. Cho trường hợp tổng quát, ký hiệu là $\tilde{\phi}(x_ n)$:

![](/content/images/2017/10/eq12.83.png)

và Gram matrix:

![](/content/images/2017/10/eq12.84.png)

Đổi sang ký hiệu matrix sẽ được:

![](/content/images/2017/10/eq12.85.png)

với $\mathbf{1}_ N$ là $N \times N$ matrix mà mỗi phần tử đều là $1/N$.

![](/content/images/2017/10/f12.17.png)

# 12.4 Nonlinear Latent Variable Models

