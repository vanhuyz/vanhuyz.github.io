+++
author = "Huy Van"
date = 2017-10-01T00:13:06Z
description = ""
draft = false
slug = "prml-chap-11-sampling-methods-11-1"
title = "PRML - Chap 11: Sampling Methods - 11.1"

+++


Bài toán là tìm kỳ vọng của hàm $f(z)$ đối với phân phối $p(z)$:

![eq11.1](/content/images/2017/09/eq11.1.png)
![f11.1](/content/images/2017/09/f11.1.png)

Ta xét bài toán là giá trị kỳ vọng này rất khó để tính bằng giải tích trực tiếp.

Ý tưởng để giải bài này là lấy các giá trị $z^{(l)} (l=1,\ldots, L)$ độc lập từ phân phối $p(z)$. Khi đó, công thức (11.1) sẽ được xấp xỉ thành tổng của các giá trị rời rạc:

![eq11.2](/content/images/2017/09/eq11.2.png)

Giá trị trung bình có kỳ vọng chính xác nhưng phương sai sẽ là:
![eq11.3](/content/images/2017/09/eq11.3.png)

Thêm 1 vấn đề trong thực tế là các giá trị $z^{(l)} (l=1,\ldots, L)$ có thể không độc lập với nhau.

# Basic Sampling Algorithms
Ở phần này, ta sẽ đề cập các phương pháp sampling. 
## Standard distributions
Các phần mềm máy tính đều dễ dàng sampling (pseudo-random) phân phối đều trong khoảng (0,1). Ta sử dụng kết quả này để sampling 1 vài phân phối đơn giản khác.

Giả sử $z$ tuân theo phân phối đều trong khoảng (0,1). $y=f(z)$ là 1 hàm số, thì phân phối $p(y)$ sẽ trở thành:

![eq11.5](/content/images/2017/09/eq11.5.png)

Trong trường hợp này $p(z)=1$. Lấy tích phân lên sẽ được:

![eq11.6](/content/images/2017/09/eq11.6.png)

Cuối cùng ta sẽ được $y=h^{-1}(z)$.

Ví dụ phân phối mũ:

$$
p(y) = \lambda \exp(-\lambda y)
$$

Sử dụng công thức sẽ được:

$$
y = -\lambda^{-1}\ln (1-z)
$$

## 11.1.2 Rejection sampling
Giờ thì $p(z)$ không phải dạng cơ bản nên không làm được theo phương pháp trên.
Giả sử có hàm $\tilde{p}(z)$ mà:

![](/content/images/2017/10/eq11.13.png)

ở đây $\tilde{p}(z)$ có thể đánh giá được nhưng $Z_ p$ thì chưa biết.

Thêm nữa có 1 phân phối đơn giản $q(z)$. Phương pháp sampling biểu diễn ở hình sau:

![](/content/images/2017/10/f11.4.png)

Công thức sẽ là:
![](/content/images/2017/10/eq11.14.png)

## 11.1.3 Adaptive rejection sampling

Hàm $q(z)$ chọn ngẫu nhiên như trên thì tỉ lệ reject cao. Nên ta dùng phương pháp cập nhật dần như sau:

![](/content/images/2017/10/f11.6.png)

