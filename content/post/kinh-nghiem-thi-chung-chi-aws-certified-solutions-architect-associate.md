+++
author = "Huy Van"
categories = ["AWS", "Vietnamese"]
date = 2019-07-08T15:08:13Z
description = ""
featured_image = "/content/images/2019/07/AWS-Certified-Solutions-Architect---Associate-certificate.jpg"
draft = false
slug = "kinh-nghiem-thi-chung-chi-aws-certified-solutions-architect-associate"
tags = ["AWS", "Vietnamese"]
title = "Kinh nghiệm thi chứng chỉ AWS Certified Solutions Architect – Associate"

+++


# Giới thiệu chung
AWS Certification là bộ chứng chỉ được câp bởi Amazon đánh giá mức độ hiểu biết về cloud (điện toán đám mây), cụ thể là các dịch vụ của Amazon Web Services (AWS)
cũng như việc áp dụng các dịch vụ đó 1 cách hiệu quả vào trong các bài toán thực tế. Bộ chứng chỉ này được chia ra theo các vai trò là Cloud Practitioner, Architect, Developer, và Operations, ngoài ra cộng thêm Specialty. Về mức độ khó thì có 3 mức độ là Foundational, Asociate và Professional. Chi tiết về các chứng chỉ các bạn tham khảo ở hình dưới đây:

<img src="/content/images/2019/07/AWS-Certification-Current-Roadmap-May2019.36b629c3b9be6da400a13e6cf1a4f9f4ef680f70.png" width=100%>
<center><small> ※ Nguồn: https://aws.amazon.com/certification/ </small></center>

AWS Certified Solutions Architect là chứng chỉ chứng nhận khả năng thiết kế hệ thống sử dụng AWS. Để đạt được chứng chỉ này, người học không những phải nắm được hết các dịch vụ của AWS mà còn phải sử dụng nó thành thạo để giải quyết trong những tình huống cụ thể. Ở mức Associate thì tương đương với 1 năm kinh nghiệm, còn Professional thì tương đương với 2 năm kinh nghiệm. Lần này mình thử thi chứng chỉ Associate.

# Tại sao mình lại thi chứng chỉ này
- Vì mình đã sử dụng AWS từ khá lâu kể cả trong công việc lẫn dự án cá nhân (chẳng hạn blog này nằm hoàn toàn trên AWS) nên cũng muốn lấy chứng chỉ để ~~chứng tỏ~~ kiểm tra trình độ của mình.
- Trong bài viết trước về [Lối đi nào dành cho các chuyên gia và kỹ sư AI?
](https://vanhuyz.com/loi-di-nao-danh-cho-cac-chuyen-gia-va-ky-su-ai/) mình thấy không cạnh tranh được với các bạn theo chiều sâu về AI nên mình quyết định đầu tư thêm về kiến thức alpha mà 1 trong số đó là kiến thức về cloud. Tương lai mình đánh giá là AI và cloud sẽ gắn liền với nhau nên skill set này cũng sẽ rất đáng giá.
- Gần đây có chứng chỉ [AWS Certified Machine Learning – Specialty](https://aws.amazon.com/certification/certified-machine-learning-specialty/) khá thiết thực mình muốn lấy ~~mà nghe nói để được thi chứng chỉ này cần tối thiểu Associate hoặc Foundational~~. Cập nhật: từ 11.10.2018 không cần Associate hoặc Foundational nữa mà có thể thi thẳng Specialty (tham khảo [nguồn](https://aws.amazon.com/about-aws/whats-new/2018/10/announcing-more-flexibility-for-aws-certification-exams/)).
- Công ty mình đang làm có chế độ cho 1 vài kỹ sư đi dự sự kiện [AWS re:Invent](https://reinvent.awsevents.com/) hàng năm ở Las Vegas nhưng muốn được đi thì tối thiểu phải có chứng chỉ Associate. Ngoài ra công ty còn có chế độ cứ 3 tháng làm việc thì cho 1 tuần được tự do làm cái mình thích nên lần này mình lấy 1 tuần vừa học vừa thi là đẹp. Phí dự thi cũng được công ty trả dù có đỗ hay không. Quá đã \^o^/

# Quá trình ôn
Mình đã có kinh nghiệm sử dụng các dịch vụ cơ bản của AWS như là EC2, S3, VPC, RDS, DynamoDB, Lambda, API Gateway, Route53... nghĩa là không phải bắt đầu từ con số 0. Đối với các bạn mới học thì có lẽ sẽ cần nhiều thời gian hơn.

- Làm thử mấy câu hỏi ví dụ trên trang chủ [tại đây](https://aws.amazon.com/certification/certified-solutions-architect-associate/).
- Tham khảo mấy blog của các bạn cùng công ty về kinh nghiệm đi thi thì được biết là cuốn [official study của AWS](https://www.amazon.co.jp/Certified-Solutions-Architect-Official-Study/dp/1119138558/) kiến thức khá cũ nên mình bỏ qua. Thay vào đó mình học theo cuốn [AWS Certified Solutions Architect Associate All-in-One Exam Guide (Exam SAA-C01) (English Edition)](https://www.amazon.co.jp/dp/B07GL9N75H)

<img src="/content/images/2019/07/20190629_201122.jpg" width="300px">

Cuốn này mình đánh giá là khá đầy đủ những kiến thức trong bài thi. Ngoài ra cuốn còn có các câu hỏi cuối chương giúp ôn tập kiến thức khá tốt. Cuốn sách dày khoảng 400 trang cũng là vừa đủ để học trong 4 ngày. Dưới đây là những kiến thức mà bạn cần nắm vững:

- Dịch vụ storage: S3, Glacier, EBS, EFS. Cần nắm vững sự khác nhau giữa các loại dịch vụ vì trong đề thi rất hay gặp. Bạn cũng cần hiểu về an toàn dữ liệu như S3 Server Side Encryption, S3 Access Control; các loại S3 theo thứ tự từ đắt tới rẻ là Standard, Standard Infrequent Access, Reduced Redundancy Storage, One Zone-Infrequent Access. Về EBS cũng cần phân biệt được SSD, HDD...

- VPC: câu hỏi về public subnet, private subnet, security group, NAT Gateway chắc chắn sẽ xuất hiện trong đề thi.

- EC2: chú ý các loại EC2 là on-demand, reserved instance, spot instance, dedicated instance. Có loại trong sách không đề cập mà trong đề thi lại có là scheduled reserved instance. Mình khuyên các bạn nên tham khảo trên [official doc mới nhất](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html) để cập nhật các loại instance vì biết đâu khi bạn thi lại có thêm loại mới.

- IAM: cần phân biệt được user, group, role.

- Auto Scaling: chú ý các loại Load Balancer (LB): classic LB, network LB, application LB

- Database: RDS, Aurora, Redshift, DynamoDB, ElastiCache. Chú ý về cơ chế Replica, Snapshot, Encryption của các dịch vụ.

- Các dịch vụ khác: Lambda, API Gateway, Kinesis, Cloudfront, Route53, AWS WAF, SQS, SNS, Beanstalk, Opswork, CloudFormation, CloudWatch, CloudTrail, AWS Config, VPC FLow Logs, Cognito, Elastic MapReduce... Đây là những dịch vụ mà mình nghĩ không phải ai cũng đã dùng hết nên các bạn cần hiểu khái niệm cơ bản về các dịch vụ này để biết nó dùng trong trường hợp nào. Chẳng hạn CloudTrail để log lại hết tất cả API call tới AWS, AWS Config để xem tất cả thay đổi liên quan đến resource configuration của AWS (EC2, security group...). Nhiều cái nó cũng na ná nhau nên bạn cần chú ý. Nếu đọc mà vẫn không thấy được sự khác biệt thì dùng thử trực tiếp là tốt nhất.

Nội dung cuốn sách chỉ là giới thiệu về các dịch vụ của AWS nhưng câu hỏi đi thi lại hỏi về ứng dụng, nghĩa là khách hàng yêu cầu hệ thống như này như này, bạn cần sử dụng dịch vụ gì để đáp ứng khách hàng đó. Mình cũng khá chủ quan không luyện thêm đề nên lúc đi thi nhiều câu phải đắn đo. Trong cuốn sách trên cũng cung cấp 1 tài khoản online để bạn luyện đề nên mình khuyên các bạn nên dành thời gian để luyện.

# Đi thi và cái kết
Bạn có thể dễ dàng đăng ký dự thi trên trang https://www.aws.training/certification. Về địa điểm thi thì mình chọn PSI Exam ở Kabukiza (歌舞伎座) gần ga Higashi-Ginza vì được khuyên là ở đó phòng ốc sạch sẽ yên tĩnh. Nhiều người đi thi ở Shibuya đều bảo là chỗ đó rất ồn nên để an toàn bạn cũng nên tránh Shibuya ra. Lệ phí thi là 16200 yên. Đề thi có 65 câu hỏi trắc nghiệm làm trong 130 phút bằng tiếng Anh (có thể chọn tiếng Nhật). Nói chung mỗi câu trung bình 2 phút nên thời gian khá thoải mái.

Ngay sau khi thi sẽ biết đỗ hay trượt. Còn điểm cụ thể thì phải vài ngày sau mới có. Điểm để đỗ là 720/1000. Mình được 867/1000 cũng may đủ để qua. Chịu khó luyện đề chắc được điểm cao hơn :(

<img src="/content/images/2019/07/AWS-Certified-Solutions-Architect---Associate-certificate.jpg" width="400px">

Sau khi có được chứng chỉ rồi thì bạn sẽ được 1 số quà tặng như là giảm giá 50% cho lần thi tới, voucher truy cập vào bộ đề luyện thi, và thú vị nhất là có code để mua sản phẩm như áo phông có chữ aws certified :))

