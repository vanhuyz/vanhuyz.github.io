+++
author = "Huy Van"
categories = ["Deep Learning", "Vietnamese"]
date = 2016-11-13T13:53:00Z
description = ""
draft = false
slug = "toi-da-tu-hoc-deep-learning-nhu-the-nao"
tags = ["Deep Learning", "Vietnamese"]
title = "Tôi đã tự học Deep Learning như thế nào"

+++


Nhân dịp TensorFlow vừa kỷ niệm [1 năm open source](https://research.googleblog.com/2016/11/celebrating-tensorflows-first-year.html), mình viết bài này để kể lại 1 năm qua mình đã tự học Deep Learning như thế nào, vừa để tự tổng hợp kiến thức, vừa để cho các bạn nào quan tâm đến Machine Learning nói chung cũng như là Deep Learning nói riêng tham khảo.

Mình là 1 kỹ sư bên server-side, công việc chủ yếu dùng Rails, SQL và hoàn toàn không liên quan gì đến Deep Learning. Mình học hoàn toàn vì sở thích và vì cũng dự đoán được phần nào những kỹ năng về mảng này sẽ rất hữu ích về sau này. Hồi đại học mình cũng có 1 năm làm nghiên cứu về nhận dạng âm thanh nên nếu nói là không có 1 chút kiến thức nào thì cũng không phải, nhưng thú thật hồi đó chỉ làm qua loa cho tốt nghiệp. Năm vừa rồi khi mà các công ty lớn tung ra hàng loạt các framework về Deep Learning, mình thấy đã đến lúc phải quay lại học cẩn thận.

## Bắt đầu từ cơ bản Machine Learning
Mình học Machine Learning qua khoá của thầy Andrew Ng trên Coursera.
Link https://www.coursera.org/learn/machine-learning

Thầy  Andrew Ng là 1 trong những người đầu ngành về cả Machine Learning và Deep Learning. Thầy Ng đã có thời gian dạy ở Stanford, 1 trong những người sáng lập Google Brain, và giờ làm Chief Scientist ở Baidu.

Khoá này thực sự rất hay cho người mới bắt đầu. Thầy Ng giảng cực kỳ chi tiết tất cả những khái niệm cần thiết, mở đầu bằng 1 ví dụ đơn giản như Linear Regression rồi đến những mô hình phức tạp hơn như Neural Network, SVM…

Qua khoá này mình đã nắm được cái lõi của Machine Learning, cũng như biết được nó có thể làm được những gì. Điều đáng tiếc 1 chút là bài tập được viết bằng Octave/Matlab - ngôn ngữ mà chủ yếu chỉ dùng trong nghiên cứu và ít khi được đưa vào các sản phẩm thực tiễn. Bạn có thể thấy khó khăn 1 chút khi học 1 ngôn ngữ hoàn toàn mới, nhưng yên tâm là Octave/Matlab khá dễ dùng và bài tập được thiết kế không quá khó.

Mình khuyên bạn nên học khoá này chung với vài người bạn, vừa để tạo động lực học, vừa để giúp đỡ nhau trong quá trình học. Thực tế là mình đã học cùng 1 vài người trong công ty và thay nhau giải thích đáp án của các bài tập: rất vui và hiệu quả.

Nếu bạn nào cảm thấy khó khăn trong việc giải các bài tập thì có thể tham khảo đáp án của mình đã được [public trên GitHub](https://github.com/vanhuyz/coursera-ml).

## Sẵn sàng cho Deep Learning
Học xong khoá Machine Learning trên chắc cũng mất khoảng 3 tháng. Sau đó mình nghỉ 1 thời gian rồi tiếp tục bước vào khoá thứ 2 về Deep Learning. Lần này mình chọn khoá Deep Learning trên Udacity do các kỹ sư của Google dạy.
Link https://www.udacity.com/course/deep-learning--ud730

Vâng, vì là các kỹ sư dạy nên mình cảm nhận là khoá này không được chi tiết như khoá của thầy Ng. Tất cả các video đều rất ngắn, và chỉ giới thiệu qua loa về các mô hình Neural Network trong Deep Learning. Mình có thể hiểu hết những gì trong video nói nhưng đến khi làm bài tập thì mới vỡ lẽ ra là chẳng hiểu gì cả :D

Cái hay của khoá này là các bài tập được thiết kế để dùng TensorFlow, rất hữu ích khi áp dụng vào thực tiễn. Nhưng nếu chỉ xem video rồi làm bài tập ngay thì mình nghĩ là khá khó để hoàn thành. Để làm được hết bài tập, mình phải làm các tutorial của TensorFlow, đọc thêm sách tham khảo, các luận văn liên quan. Nói chung là video thì chắc chỉ 2 tuần là xem hết, nhưng mà giải được hết bài tập thì cũng phải mất 3 tháng.

Đáp án mình cũng [công khai hết trên GitHub](https://github.com/vanhuyz/udacity-dl).

## Đào sâu thêm về Deep Learning
Nói chung là sau khoá trên thì mình cũng "hiểu na ná" CNN, RNN là gì rồi. Nhưng để áp dụng vào thực tế thì thấy vẫn còn khá khó khăn. Mình bắt đầu đọc sách mà được coi là [sách giáo khoa của Deep Learning](https://www.amazon.com/Deep-Learning-Adaptive-Computation-Machine/dp/0262035618/). Sách có thể đọc online tại http://www.deeplearningbook.org/

Sách này do Goodfellow, Bengio et al - những người đầu ngành về Deep Learning viết. Cuốn này khá dày nên mình đã in ra đóng thành sách giấy để đọc cho tiện. Chương 1 là kiến thức cơ bản về toán và Machine Learning. Chương này bổ sung rất tốt kiến thức cho khoá học Coursera, giúp mình hiểu rõ thêm về bản chất toán cũng như sự liên quan đến xác suất thống kê của các thuật toán đã học. Chương 2 là chương mình thích nhất. Chương này giải thích rất chi tiết về các Neural Network thường dùng như CNN, RNN, cũng như các ứng dụng của nó vào thực tế. Chương này thực sự giúp mình lấp lỗ hổng kiến thức sau khoá học trên Udacity. Chương cuối cùng là dành cho các nhà nghiên cứu. Rất khó để hiểu hết, nên hiện tại mình chỉ đọc với mục đích nhớ tên khái niệm là chính, hy vọng sau này có thể hiểu :D

Song song với việc đọc sách giáo khoa về Deep Learning, mình còn xem video các bài giảng về Deep Learning của đại học Stanford. Có 2 khoá được công khai:

- Khoá về xử lý hình ảnh: http://cs231n.stanford.edu/

- Khoá về xử lý ngôn ngữ tự nhiên: http://cs224d.stanford.edu/

2 khoá này đều rất hay, nhưng thú thực là video dài quá và nhiều cái mình nghĩ đã hiểu đủ qua sách của Goodfellow nên mình không xem hết, mà chỉ tập trung những cái mà mình quan tâm thôi.

## Bắt đầu tự viết ứng dụng
Sau khoảng 10 tháng để tiêu hoá hết chỗ trên, mình bắt đầu dùng TensorFlow để làm những ứng dụng chơi chơi đơn giản. Ứng dụng gần đây nhất là viết 1 con chatbot trên LINE  có thể gửi trả 1 sticker phù hợp với nội dung tin nhắn.  Ứng dụng vẫn đang trong quá trình phát triển vì vẫn còn hơi ít data nhưng mình đã phát biểu về nó tại 1 meetup cho engineer ở Nhật. Bạn nào hiểu tiếng Nhật có thể tham khảo tại 
http://www.slideshare.net/vanhuyz/tensorflowline-botaws-lambda

Hiện nay công ty mình bắt đầu có 1 vài dự án mới về AI, và những kiến thức mình thu được 1 năm qua chắc chắn sẽ giúp ích được rất nhiều.

## Tóm lại
Deep Learning là 1 mảng khó, nên bạn không thể học hết trong 1 vài ngày. Nếu muốn làm việc về lĩnh vực này, bạn cần thời gian và cả 1 chút đam mê nữa. Hy vọng qua bài viết của mình, các bạn có thể tự tìm được phương pháp học tốt nhất cho mình!

Thân ái,

※ Bài viết này mình cũng đã đăng trên [Kipalog](https://kipalog.com/posts/Toi-da-tu-hoc-Deep-Learning-nhu-the-nao)

