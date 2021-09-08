+++
author = "Huy Van"
categories = ["AWS", "Hugo"]
date = 2019-08-05T22:55:00Z
description = ""
draft = false
slug = "dung-aws-amplify-de-deploy-website-tinh-viet-bang-hugo"
tags = ["AWS", "Hugo"]
title = "Dùng AWS Amplify để deploy website tĩnh viết bằng Hugo"

+++


# Hugo là gì?
[Hugo](https://gohugo.io/) là 1 framework viết bằng Go dùng để tạo trang web tĩnh như trang chủ công ty, trang giới thiệu sản phẩm... Cách sử dụng hết sức đơn giản, chỉ cần download 1 theme có sẵn về, sửa nội dung theo hướng dẫn rồi gõ `hugo` để dịch sang html là bạn đã có 1 trang web tĩnh rất nhanh chóng. 

Đây là ví dụ 1 trang web tạo từ Hugo: https://vjai.jp
Source code được chia sẻ tại đây: https://github.com/vjai-community/vjai.jp 

# AWS Amplify là gì?
Sau khi đã có tập các file html cho trang web của bạn thì bước tiếp theo là deploy lên production. Cách truyền thống là thuê 1 server rồi deploy lên đó, nhưng như vậy sẽ tốn tiền server và công quản lý cho 1 trang web rất bé như thế này. Rất may là AWS hỗ trợ hosting trang web tĩnh sử dụng S3 với giá rất rẻ, và có [AWS Amplify](https://aws.amazon.com/amplify/) để tự động hóa việc deploy. 

0. Chuẩn bị: bạn cần up source code Hugo của mình lên GitHub như [link ở trên](https://github.com/vjai-community/vjai.jp). Public hay private đều được.

1. Bạn đăng nhập vào [AWS Amplify Console](https://console.aws.amazon.com/amplify/home) và chọn `Get started` trong mục `Deploy` như sau:

![](/content/images/2019/07/amplify_start.png)

2. Chọn `Github` và cho phép AWS Amplify có quyền đọc source code như bên dưới:

![](/content/images/2019/07/connect_github.png)

![](/content/images/2019/07/github_allow.png)

![](/content/images/2019/07/github_success.png)

3. Bước tiếp theo là chọn `Hugo` ở mục framework và viết vài lệnh cần thiết để deploy. Amplify sẽ tự động sinh code mẫu cho bạn. Nếu không có gì thay đổi thì cứ vậy click tiếp là được.  

![](/content/images/2019/07/build_settings.png)

```
version: 0.1
frontend:
  phases:
    build:
      commands:
        - wget https://github.com/gohugoio/hugo/releases/download/v0.56.1/hugo_extended_0.56.1_Linux-64bit.tar.gz
        - tar -xf hugo_extended_0.56.1_Linux-64bit.tar.gz hugo
        - mv hugo /usr/bin/hugo
        - rm -rf hugo_extended_0.56.1_Linux-64bit.tar.gz
        - hugo
  artifacts:
    baseDirectory: public
    files:
      - '**/*'
  cache:
    paths: []
```


Ở đây vì cái theme mình sử dụng yêu cầu dịch `scss` sang `css` nên bắt buộc phải dùng bản `extended` nên mình thêm đoạn code để down bản đó về. Nếu bạn chọn theme khác thì có thể bản bình thường cũng được, nghĩa là không cần thay đổi code mặc định.

Để lấy được link down Hugo phiên bản mới nhất thì bạn vào https://github.com/gohugoio/hugo/releases. 

Sau bước này chờ 1 chút cho Amplify làm việc. Gặp lỗi thì bạn đọc log rồi sửa. Nếu thành công thì sẽ xuất hiện hình như bên dưới:

![](/content/images/2019/07/build.png)

Tới bước này là bạn đã có thể xem trang web của mình trên URL mà Amplify tự sinh ra ở trên (có dạng `master...amplify.com`).

4. Gắn trang web vào domain của bạn. Bạn mua domain ở đâu thì vào vào đó để cài đặt A record (với Alias) hoặc CNAME record để trỏ về link amplify.com ở trên. Cách khác là chuyển nameserver về cho Route53 quản lý rồi có gì thao tác hết trên AWS cho tiện cũng được.

Trong trường hợp của mình thì nếu mua trên Onamae thì sẽ chuyển nameserver cho Route53 quản lý vì trang Oname với giao diện tiếng Nhật rất khó dùng. Còn NameCheap thì dễ dùng hơn nên mình thường để vậy luôn không cần tới Route53. 

![](/content/images/2019/07/add_domain.png)

Cách cài đặt với Route53 bạn tham khảo ở đây: https://docs.aws.amazon.com/amplify/latest/userguide/custom-domains.html

Về cách cài đặt với dịch vụ cung cấp domain thông dụng như GoDaddy, Google Domain không thông qua Route53 bạn tham khảo ở đây: https://docs.aws.amazon.com/amplify/latest/userguide/howto-third-party-domains.html 

Sau đó click tiếp vài cái theo hướng dẫn để cài đặt SSL (nếu không xài Route53 bạn sẽ phải copy ra trang quản lý domain tạo 1 CNAME record mới để xác nhận) thì bạn sẽ tới được màn hình bên dưới:

![Screenshot-from-2019-08-06-02-23-46](/content/images/2019/08/Screenshot-from-2019-08-06-02-23-46.png)

Tới đây thì bạn có thể vào domain mà bạn đã mua và xác nhận nó đã trỏ về trang web thành công! Từ giờ trở đi cứ khi nào code được merge vào master branch thì Amplify sẽ tự động deploy cho bạn rất tiện.

# Bonus
Thế sử dụng Amplify có tốn tiền không chắc là câu hỏi của nhiều người. Mặc dù để cài đặt Amplify chỉ cần click là xong nhưng thực tế  bên dưới Amplify sử dụng S3 để lưu dữ liệu, AWS CodeBuild để dịch code và CloudFront như 1 CDN (bạn có thể kiểm tra từng dịch vụ để thấy những gì được tự động tạo ra). Phí sử dụng như sau:

- Thời gian deploy ($0.01 per build minute)
- Dữ liệu lưu trên S3 ($0.023 per GB stored per month)
- Dữ liệu truyền qua CloudFront ($0.15 per GB served)

Thế nên nếu sử dụng làm trang chủ công ty chẳng hạn, 1 tuần deploy 1 lần, với khoảng 100 người ghé qua hàng ngày thì mình ước tính tốn khoảng $4, khá rẻ so với việc thuê 1 server riêng.

Vậy nếu tôi không muốn sử dụng CloudFront để giảm chi phí thì sao? Amplify dễ dùng thật nhưng không dễ tùy biến nên hiện tại mình chưa tìm được cách để cắt CloudFront. Thế nên nếu muốn thiết kế theo ý mình thì các bạn có thể tự cài đặt CodeBuild thay vì dùng Amplify. 

# Tóm lại
Amplify là dịch vụ rất hay dùng để hosting và deploy tự động trang web đơn giản mà hiệu quả. Chỉ với vài click đơn giản bạn đã làm được những việc như sau:

- Sử dụng AWS CodeBuild để nhận webhook từ GitHub để khi nào có thay đổi sẽ tự động dịch code
- Thành quả là những file html sẽ được up tự động lên S3
- CloudFront sẽ đóng vai trò như CDN để cung cấp nội dung từ S3 tới client thông qua https với domain được quản lý bởi Route53 và AWS Certificate Manager

# Tham khảo
https://docs.aws.amazon.com/amplify/latest/userguide/getting-started.html
https://gohugo.io/hosting-and-deployment/hosting-on-aws-amplify/
https://dev.classmethod.jp/cloud/aws/hugo-hosted-on-aws-amplify-console/

