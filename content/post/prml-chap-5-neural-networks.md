+++
author = "Huy Van"
categories = ["PRML", "Vietnamese"]
date = 2017-04-29T09:23:29Z
description = ""
draft = false
slug = "prml-chap-5-neural-networks"
tags = ["PRML", "Vietnamese"]
title = "PRML - Chap 5: Neural networks"

+++


## 5.5.3 Invariances

Trong bài toán thực tế, nhiều lúc kết quả dự đoán không đổi cho dù đầu vào có nhứng biến đổi nhất định. Chẳng hạn trong bài toán phân biệt ảnh chó với ảnh mèo thì

* còn mèo có nằm ở góc nào của ảnh thì vẫn là con mèo (translation invariance)
* kích cỡ ảnh hay con mèo có to nhỏ cỡ nào thì vẫn là con mèo (scale invariance)

Dữ liệu mà có cực nhiều với đầy đủ các patterns thì máy có thể tự học được các thay đổi đó nhưng thực tế thì không như vậy.

---

### 4 cách giải quyết

1. Tự nhân bản dữ liệu học bằng việc thêm nhiễu
2. Regularization
3. Chọn feature hợp lý ko phụ thuộc vào variance
4. Chọn cấu trúc network hợp lý 

---

### Ví dụ cách 1
Nhân bản ảnh bằng cách thêm nhiễu rồi convolution với Gaussian
<img width="937" alt="Screen Shot 2017-04-22 at 16.10.52.png" src="https://galapagos.qiita.com/files/bfa6b563-e22d-d1d8-2fe3-5f985d4d1695.png">

---

### Ví dụ cách 2: regularization 

* Dùng Tangent propagation: cải tiến back propagation giúp network khoẻ hơn với variance (nội dung của 5.5.4)
* Transform data theo 1 nhiễu nào đó cuối cùng dẫn ra được công thức giống regularization (nội dung của 5.5.5)

---

### Ví dụ cách 4: 5.5.6 Convolutional networks

[Tham khảo thêm ở bài giảng của cs231n - Stanford](http://cs231n.stanford.edu/slides/2017/cs231n_ 2017_ lecture5.pdf)

---

## 5.5.7 Soft weight sharing

* Kỹ thuật này có thể coi là trường hợp tổng quát của weight decay
* Nhớ lại là weight decay là áp penalty vào tất cả các weight. Việc này tương đương với chọn prior là 1 Gaussian. 
* Bây giờ ta không muốn penalty giống nhau cho tất cả các weight mà chia weight ra thành các group. Trong mỗi group thì áp dụng Gausian riêng. Điều này tương đương với việc chọn prior là 1 mixture of Gaussians:

$$
p(\mathbf{w}) = \sum_ i p(w_ i)
$$

với 

$$
p(w_ i) = \sum_ {j=1}^M \pi_ j \mathcal{N} (w_ i|\mu_ j, \sigma_ j^2)
$$

Ở đây $\pi_ j$ là hệ số mixing.

Lấy negative log của hàm này ta sẽ dẫn ra được hàm regularization như sau:

$$
\Omega(\mathbf{w}) = -\sum\ln\Big(\sum_ {j=1}^M \pi_ j\mathcal{N}(w_ i|\mu_ j,\sigma_ j^2\Big)
$$

Vì thế total error function sẽ trở thành:

$$
E(\mathbf{w})=E(\mathbf{w})+\lambda\Omega(\mathbf{w})
$$

Minimize hàm này có thể dùng các phương pháp optimization tiêu chuẩn như conjugate gradients hay quasi-Newton.

