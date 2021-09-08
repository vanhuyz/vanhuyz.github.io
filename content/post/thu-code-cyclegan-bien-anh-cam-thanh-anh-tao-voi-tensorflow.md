+++
author = "Huy Van"
categories = ["Deep Learning", "Vietnamese"]
date = 2017-05-02T11:30:02Z
description = ""
draft = false
slug = "thu-code-cyclegan-bien-anh-cam-thanh-anh-tao-voi-tensorflow"
tags = ["Deep Learning", "Vietnamese"]
title = "Thử code CycleGAN biến ảnh cam thành ảnh táo với TensorFlow"

+++


## Giới thiệu về CycleGAN

Trước hết mời các bạn xem video này:

<iframe width="560" height="315" src="https://www.youtube.com/embed/9reHvktowLY" frameborder="0" allowfullscreen></iframe>

Đây là thuật toán sử dụng Deep Learning để chuyển từ ảnh này sang ảnh kia mà vẫn giữ nguyên bố cục, chỉ thay đổi bề mặt của vật thể. Chẳng hạn chuyển từ ảnh ngựa thường sang ngựa vằn, chuyển ảnh cam thành ảnh táo, chuyển ảnh thành tranh... Không chỉ áp dụng với ảnh mà còn cả với video nữa, và như tác giả có nói là có thể realtime với video 60fps! :wow:

Để biết thêm các ứng dụng của CycleGAN, các bạn có thể tham khảo ở trang chủ: https://junyanz.github.io/CycleGAN/.

Cái hay của CycleGAN là không yêu cầu phải có cặp ảnh với bố cục giống nhau để làm dữ liệu học, mà chỉ cần rất nhiều ảnh phân chia thành 2 tập, chẳng hạn 1 tập toàn ảnh ngựa thường, 1 tập toàn ảnh ngựa vằn, và máy sẽ tự động nhận dạng ngựa thường để chuyển hoá nó thành ngựa vằn.

Luận văn gốc của CycleGAN tại đây: https://arxiv.org/abs/1703.10593

Tác giả của CycleGAN cũng đã công khai toàn bộ source code viết bằng Torch (1 framework Deep Learning bằng ngôn ngữ Lua) trên [GitHub](https://github.com/junyanz/CycleGAN).

## Code lại bằng TensorFlow

Nhằm hiểu rõ hơn về thuật toán rất "cool" này, mình đã tự code lại toàn bộ bằng TensorFlow.

Code mình công khai tại đây:
https://github.com/vanhuyz/CycleGAN-TensorFlow

Sau đây mình sẽ hướng dẫn cách để các bạn có thể sử dụng mô hình CycleGAN mà mình đã huấn luyện.

* Chuẩn bị môi trường: <br>
Bạn cần cài đặt Python 3.6 trở lên và TensorFlow phiên bản 1.0 trở lên. Các bước cài đặt mình xin phép lược bỏ.

* Download source code: <br>
Sau khi chuẩn bị môi trường xong xuôi, bạn cần clone repository về:
```
$ git clone git@github.com:vanhuyz/CycleGAN-TensorFlow.git
```
* Sau đó download mô hình đã huấn luyện tại đây:
https://github.com/vanhuyz/CycleGAN-TensorFlow/releases

Hiện tại mới có mô hình biến cam thành táo và ngược lại. Kích thước mỗi mô hình rất nhẹ, chỉ 4.17MB.

* Thử chuyển ảnh cam thành táo với câu lệnh sau:

```
$ python3 inference.py --model orange2apple.pb \
                     --input input_sample.jpg \
                     --output output_sample.jpg \
```

Chú ý là mô hình hiện tại của mình chỉ chấp nhận ảnh đầu vào là JPEG với kích cỡ 256x256.

Sau đây là 1 vài kết quả tốt. Tất nhiên các ảnh này không chứa trong tập huấn luyện.

<table>
<tr>
<td style="text-align: center;">Ảnh gốc</td>
<td style="text-align: center;">Ảnh ra</td>
</tr>
<tr>
<td><img src="/content/images/2017/05/real_orange2apple_4.jpg" alt="real_orange2apple_4" style="width:304px;height:228px;">
</td>
<td>
<img src="/content/images/2017/05/fake_orange2apple_4.jpg" alt="real_orange2apple_4" style="width:304px;height:228px;">
</td>
</tr>
<tr>
<td><img src="/content/images/2017/05/real_orange2apple_3.jpg" alt="real_orange2apple_3" style="width:304px;height:228px;">
</td>
<td>
<img src="/content/images/2017/05/fake_orange2apple_3.jpg" alt="real_orange2apple_3" style="width:304px;height:228px;">
</td>
</tr>
<td><img src="/content/images/2017/05/real_orange2apple_2.jpg" alt="real_orange2apple_2" style="width:304px;height:228px;">
</td>
<td>
<img src="/content/images/2017/05/fake_orange2apple_2.jpg" alt="real_orange2apple_2" style="width:304px;height:228px;">
</td>
</tr>
</table>

Cái vỏ sần sùi của quả cam đã được biến khéo léo thành vỏ nhẵn bóng của quả táo. Rất kỳ diệu :D

## 1 vài cảm tưởng khi tự tay code
* Nhiều chỗ luận văn viết 1 đằng nhưng code của tác giả lại theo 1 nẻo. Đúng là luận văn trên arXiv vẫn chưa được review nên không thể tin tưởng 100% được. Cũng may tác giả công khai code nếu không thì chắc cả đời không hiểu sao mình sai :persevere:
* Huấn luyện mô hình này khá vất vả. Nhiều khi cắm máy chạy 1 đêm xong sáng ra xem kết quả thì thấy ảnh biến ra linh tinh lại phải cho chạy lại. 
* Sắp tới mình đang định thử cho ảnh Songoku tóc đen vào và thử biến thành siêu Xay Da xem sao :D

## Tóm lại
Trong bài viết này mình không đi sâu vào chi tiết thuật toán mà chỉ mang tính chất giới thiệu. Có thể mình sẽ đi sâu vào giải thích thuật toán kỹ hơn trong bài viết sau.

Deep Learning là 1 công nghệ khó nhưng hiện nay có rất nhiều ứng dụng cực kỳ thú vị trong thực tế. Cũng như học bất cứ công nghệ nào mới, cách học tốt nhất là "tự tay nhúng chàm": học lý thuyết rồi tự code.

Nhiều chỗ mình cũng không chắc có hiểu đúng hay không, nếu bạn nào đọc rồi mà thấy mình code sai thì mở issue trên GitHub để mình sửa. Nếu có pull request luôn thì quá tuyệt!

