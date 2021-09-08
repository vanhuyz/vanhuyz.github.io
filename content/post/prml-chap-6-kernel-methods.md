+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-06-11T13:12:16Z
description = ""
draft = false
slug = "prml-chap-6-kernel-methods"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 6: Kernel methods - 6.4 Gaussian Processes"

+++


## 6.4.1 Linear regression revisited
Thử xem xét lại model:

$$
y(\mathbf{x}) = \mathbf{w}^T\phi(\mathbf{x})
$$

với $\mathbf{x}$ là input, $y(\mathbf{x})$ là output, $\mathbf{w}$ là parameters, $\phi(\mathbf{x})$ là basis function.

Giả sử prior đối với $\mathbf{w}$ là 1 Gaussian đơn vị:

$$
p(\mathbf{w}) = \mathcal{N}(\mathbf{w}|\mathbf{0},\alpha^{-1}\mathbf{I})
$$

Trong thực tế, với training set $x_ 1,\ldots,x_ N$, ta quan tâm tới 

$$
\mathbf{y} = \big(y(x_ 1),\ldots,y(x_ N)\big)^T = \mathbf{\Phi}\mathbf{w}
$$

với $\mathbf{\Phi}$ là ma trận với mỗi phần tử là $\Phi_ {nk} = \phi_ k(x_ n)$
($\phi_ k$ là gì thì chưa rõ, có thể sách in sai?). 

$\mathbf{y}$ là tổ hợp tuyến tính của các Gausian nên sẽ là 1 Gaussian, với mean và covariance như sau:

$$
\mathbb{E}[\mathbf{y}] = \mathbf{\Phi}\mathbb{E}[\mathbf{w}] = 0\\\
\mathrm{cov}[\mathbf{y}] = \mathbb{E}[\mathbf{y}\mathbf{y}^T] = \mathbf{\Phi}\mathbb{E}[\mathbf{w}\mathbf{w}^T]\Phi^T = \frac{1}{\alpha} \mathbf{\Phi}\mathbf{\Phi^T} = \mathbf{K}
$$

Ở đây $K$ là ma trận Gram với các phần tử là

$$
K_ {nm} = k(x_ n, x_ m) = \frac{1}{\alpha} \phi(x_ n)^T \phi(x_ m)
$$

và $k(x,x')$ là kernel function.

Kết luận Gaussian process là 

$$
p(\mathbf{y}) = \mathcal{N}(\mathbf{y}|0, \mathbf{K})
$$

## 6.4.2 Gaussian processes for regression

Với bài toán regression, label $t_ n$ sẽ được tính bởi:

$$
t_ n = y_ n + \epsilon_ n
$$

với $y_ n = y(x_ n)$ và $\epsilon_ n$ là random noise. Giả sử quá trình thêm noise là 1 phân phối Gaussian:

$$
p(t_ n|y_ n) = \mathcal{N}(t_ n|y_ n, \beta^{-1})
$$

Noise với mỗi data point độc lập với nhau nên nếu đặt $\mathbf{t} = (t_ 1, \ldots, t_ N)^T$, $\mathbf{y} = (t_ 1, \ldots, t_ N)^T$ thì:

$$
p(\mathbf{t}|\mathbf{y}) = \mathcal{N}(\mathbf{t}|\mathbf{y}, \beta^{-1}\mathbf{I}_ N)
$$

trong đó $\mathbf{I}_ N$ là ma trận đơn vị $N\times N$. 

Từ định nghĩa của Gaussian process, 

$$
p(\mathbf{y}) = \mathcal{N}(\mathbf{y}|0, \mathbf{K})
$$

Maginal distribution $p(\mathbf{t})$ sẽ được tính bằng:

$$
p(\mathbf{t}) = \int p(\mathbf{t}|\mathbf{y}) p(\mathbf{y}) \mathrm{d}\mathbf{y} = \mathcal{N}(\mathbf{t}|\mathbf{0}, \mathbf{C})
$$

với ma trận covariance $\mathbf{C}$ có các phần tử là:

$$
C(x_ n, x_ m) = k(x_ n, x_ m) + \beta^{-1}\sigma_ {nm}.
$$

Đặt training set $\mathbf{t}_ N = (t_ 1,\ldots,t_ N)$ gắn với input $x_ 1,\ldots, x_ N$. Mục tiêu của chúng ta là dự đoán $t_ {N+1}$ tại 1 điểm hoàn toàn mới $x_ {N+1}$.

$$
p(\mathbf{t}_ {N+1}) = \mathcal{N}(\mathbf{t}_ {N+1}|\mathbf{0}, \mathbf{C}_ {N+1})
$$

với $\mathbf{C}_ {N+1}$ là ma trận covariance có chiều là $(N+1) \times (N+1)$.

Ta có thể tính được:

$$
\mathbf{C}_ {N+1} = \left(
\begin{matrix}
\mathbf{C}_ N & \mathbf{k} \\\
\mathbf{k}^T & c 
\end{matrix}
\right)
$$

Ở đây vector $\mathbf{k}$ có các phần tử là $k(x_ n, x_ {N+1})$ for $n=1,\ldots,N$ và scalar $c = k(x_ {N+1}, x_ {N+1}) + \beta^{-1}$.

Sử dụng các kết quả từ chương 2, ta cũng tìm được conditional distribution $p(t_ {N+1}|\mathbf{t})$ là 1 Gaussian với mean và covariance như sau:

$$
m(x_ {N+1}) = \mathbf{k}^T\mathbf{C}_ N^{-1}\mathbf{t} \\\
\sigma^2(x_ {N+1}) = c - \mathbf{k}^T\mathbf{C}_N^{-1}\mathbf{k}
$$

※ **6.4.3 Learning the hyperparameters**
và **6.4.4 Automatic relevance determination** nội dung không quá quan trọng nên giản lược.

