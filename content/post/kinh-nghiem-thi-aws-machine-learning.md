+++
author = "Huy Van"
categories = ["Machine Learning", "AWS", "Vietnamese", "AI"]
date = 2019-07-22T12:14:03Z
description = ""
draft = false
slug = "kinh-nghiem-thi-aws-machine-learning"
tags = ["Machine Learning", "AWS", "Vietnamese", "AI"]
title = "Kinh nghiệm thi AWS Certified Machine Learning – Specialty"

+++


Tiếp theo kinh nghiệm thi chứng chỉ [Solutions Architect - Associate](https://vanhuyz.com/kinh-nghiem-thi-chung-chi-aws-certified-solutions-architect-associate/) lần trước thì lần này là chứng chỉ [Machine Learning – Specialty](https://aws.amazon.com/certification/certified-machine-learning-specialty/). Để đạt được chứng chỉ này bạn cần có những kiến thức cơ bản về Machine Learning (ML), khả năng ứng dụng ML vào các bài toán thực tế cũng như sử dụng thành thạo các dịch vụ liên quan tới ML của AWS. 

Các bạn có thể tham khảo câu hỏi mẫu tại [đây](https://d1.awsstatic.com/training-and-certification/docs-ml/AWS%20Certified%20Machine%20Learning%20-%20Specialty_Sample%20Questions.pdf).

Chứng chỉ ML là 1 trong các chứng chỉ mới nhất của AWS (cùng với Alexa Builder), mới chính thức hoạt động từ tháng 3/2019. Lệ phí dự thi là 32000 yên ($300) nhưng giảm giá 1 nửa cho người đã có 1 chứng chỉ khác của AWS. Thế nên mình chỉ phải trả 16000 yên, bằng tiền cái chứng chỉ Associate trước. Dù sao công ty cũng sẽ trả nên coi như tiết kiệm được tiền cho công ty.

Nội dung bài thi gồm các phần như sau (copy từ [offical guide](https://d1.awsstatic.com/training-and-certification/docs-ml/AWS%20Certified%20Machine%20Learning%20-%20Specialty_Exam%20Guide%20(1).pdf)):

###### Domain 1: Data Engineering (20%)
1.1 Create data repositories for machine learning.
1.2 Identify and implement a data-ingestion solution.
1.3 Identify and implement a data-transformation solution.
###### Domain 2: Exploratory Data Analysis (24%)
2.1 Sanitize and prepare data for modeling.
2.2 Perform feature engineering.
2.3 Analyze and visualize data for machine learning.
###### Domain 3: Modeling (36%)
3.1 Frame business problems as machine learning problems.
3.2 Select the appropriate model(s) for a given machine learning problem.
3.3 Train machine learning models.
3.4 Perform hyperparameter optimization.
3.5 Evaluate machine learning models.
###### Domain 4: Machine Learning Implementation and Operations (20%)
4.1 Build machine learning solutions for performance, availability, scalability, resiliency, and fault tolerance.
4.2 Recommend and implement the appropriate machine learning services and features for a given problem.
4.3 Apply basic AWS security practices to machine learning solutions.
4.4 Deploy and operationalize machine learning solutions.

## Ôn luyện

Về ML thuần tuý thì mình không ôn luyện gì thêm. Các câu hỏi đều ở dạng cơ bản nên các bạn có thể tham khảo blog trước [Tôi đã tự học Deep Learning như thế nào](https://vanhuyz.com/toi-da-tu-hoc-deep-learning-nhu-the-nao/) của mình. Cái khó của kỳ thi này đối với mình chính là phần sử dụng thành thạo các dịch vụ của AWS.

Dưới đây là những tài liệu mình đã tham khảo.

##### Video từ chính AWS (aws.training)

* Machine Learning Exam Basics: https://www.aws.training/learningobject/curriculum?id=27271

* Developing Machine Learning Applications: 
https://www.aws.training/learningobject/curriculum?id=27243

##### Practice exam
Phí dự thi 1 lần là 4000 yên với 20 câu hỏi trong 60 phút. Miễn phí 1 lần cho người đã có chứng chỉ khác của AWS (quá tiện). Mình sử dụng để tham khảo xu hướng của đề thi.

##### Khoá học AWS Certified Machine Learning – Specialty trên acloud.guru
 
https://learn.acloud.guru/course/aws-certified-machine-learning-specialty/dashboard

acould.guru cho miễn phí trong 7 ngày nên để tiết kiệm tiền các bạn cố gắng học cấp tốc trong 7 ngày là ổn. Mình không xem hết nhưng thấy cái exam tips của họ rất hữu ích.

---
Dưới đây là những gì mình tổng hợp lại vì có thể có ích.

#### 1. Các dịch vụ cuối (application services)

- Polly: chuyển text thành speech
- Lex: xây dựng ứng dụng chatbot
- Rekoginition: phân tích ảnh và video sử dụng Deep Learning
- Transcribe: chuyển speech thành text
- Translate: dịch từ ngôn ngữ này sang ngôn ngữ khác
- Comprehend: dịch vụ về xử lý ngôn ngữ tự nhiên. Chẳng hạn nó có thể nhận biết thực thể (entities), quan hệ (relations), tâm lý (sentiments) trong đoạn văn bản

#### 2. Các dịch vụ nền tảng (platform services)

- SageMaker
- DeepLens 
- AWS Deep Learning AMI
- MXNet/Gluon

SageMaker có thể nói là trung tâm của hệ sinh thái AWS liên quan tới ML. SageMaker giúp quản lý jupyter notebook, với rất nhiều các thuật toán built-in, giúp tự động tunning hyperparameter theo Baysian Optimization. Nó giúp việc deploy chỉ với 1-click, quản lý model traffic và có auto-scaling. Nói chung mình cảm nhận SageMaker được AWS đầu tư rất nhiều, tới mức mà nói về ML on AWS là nói tới SageMaker nên trong bài thi có 1 phần không nhỏ về thằng này. 

Để hiểu kỹ hơn về SageMaker tham khảo thêm ở [Developer Guide](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html). 

Trong các thuật toán built-in của SageMaker có 1 thuật toán khá thú vị là [Random Cut Forrest cho Anomaly Detection](https://docs.aws.amazon.com/sagemaker/latest/dg/randomcutforest.html). RCF đã được đăng trên ICML2016 và Amazon có vẻ khá tự hào về thằng này nên đã tích hợp luôn vào hệ thống. RCF cũng xuất hiện trong một vài câu hỏi trong bài thi. Để hiểu thêm về thuật toán này các bạn tham khảo thêm ở video trong aws.training: https://www.aws.training/learningobject/video?id=27226


Trước khi thi mình cũng có nghe nhiều về SageMaker nhưng việc áp dụng vào công ty mình thì mình thấy khá xa vời nên không tìm hiểu kỹ lắm. Chẳng hạn mình không đồng tình lắm với việc train trên jupyter notebook rồi đưa lên production luôn. Mình có xu hướng chuyển thành python script, chia class, viết test, thêm log nếu cần thiết rồi mới cho lên production. Đọc SageMaker guide thì thấy nó cũng support việc train mô hình bên ngoài rồi up lên SageMaker sau nhưng nếu làm vậy thì cho lên 1 con ECS bình thường vẫn nhanh hơn. Rất tiếc là dịp này chưa kịp trên tay SageMaker kỹ nhưng mình sẽ cố gắng tìm hiểu sâu hơn để có bài review chi tiết. 

#### 3. Các dịch vụ liên quan đến dữ liệu 
##### AWS Migration Tools

* Data Migration Service
* Data Pipeline
* Glue

3 dịch vụ này giúp chuyển dữ liệu từ on-primise hoặc AWS resource tới AWS resource khác. Nói thật là mình vẫn chưa hiểu Data Pipeline với Glue khác nhau thế nào nên cũng học vẹt để đi thi :(

 
##### AWS Data helpers

* Athena: giúp query SQL trên S3! (quá mạnh)
* EMR (Elastic MapReduce): hadoop-like eco system
* SageMaker Ground Truth:  dịch vụ để gắn nhãn cho dữ liệu

#####  Streaming Data 
hay còn gọi là Kinesis Family

- Kinesis Data Streams: xử lý dữ liệu stream. Có thể lưu dữ liệu trong 24h (up lên tới 7 ngày). Người dùng phải thiết lập các shard (tưởng tượng như stream là 1 đoàn tàu thì shard là toa tàu còn hành khách là dữ liệu).
- Kinesis Data Firehose: dữ liệu không được lưu lại mà sẽ được chuyển sang 1 chỗ chứa dữ liệu khác (S3, Redshift, ES). Sử dụng đơn giản không phải lo tới thiết lập shard.
- Kinesis Data Analytics: giúp chạy SQL trên streaming data

Nói thật là Data Streams với Data Firehose cực kỳ giống nhau nên cần chú ý đọc thêm tài liệu để hiểu. Cái này mình cũng không nắm vững lắm nên ôn tủ mấy câu để đi thi. Trong bài thi gặp rất nhiều!

## Đi thi và cái kết

Mình chuẩn bị cho kỳ thi này khá cấp tốc (bắt đầu học từ thứ 6 mà CN đi thi luôn) nên không có thời gian sử dụng hết các dịch vụ trên. Đến khi đi thi vẫn lơ mơ về Kinesis và SageMaker. Nhưng rất may trong kỳ thi có nhiều câu hỏi về ML cơ bản nên cũng kiếm được nhiều điểm từ phần này. Bài thi có 65 câu hỏi trắc nghiệm làm trong 180 phút (3 tiếng!). Thời gian khá thoải mái nhưng nhiều câu hỏi phải suy nghĩ nhiều nên cũng khá căng thẳng.

Để đỗ thì cần đạt được 750/1000. Tiêu chuẩn cao hơn kỳ thi Associate là chỉ cần 720. Mình được 838/1000. Phew!

Kết quả cụ thể như ở dưới:

<img src="/content/images/2019/07/Screen-Shot-2019-07-22-at-18.55.39.png" width="60%">

Phần Exploratory Data Analysis cần cải thiện thêm. Đúng là như vậy. Mình vẫn còn yếu trong việc dùng biểu đồ để nhìn dữ liệu. Nhưng dù sao cũng pass, coi như là hoàn thành mục tiêu.

<img src="/content/images/2019/07/AWS-Certified-Machine-Learning---Specialty-certificate.jpg" width="60%">

