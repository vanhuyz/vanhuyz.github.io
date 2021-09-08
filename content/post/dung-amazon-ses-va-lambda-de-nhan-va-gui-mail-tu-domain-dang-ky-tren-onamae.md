+++
author = "Huy Van"
date = 2019-07-26T12:39:53Z
description = ""
draft = true
slug = "dung-amazon-ses-va-lambda-de-nhan-va-gui-mail-tu-domain-dang-ky-tren-onamae"
title = "Dùng Amazon SES và Lambda để nhận và forward email tới gmail"

+++


1. Tạo 1 *Public Hosted Zone* bằng Amazon Route53

2. Đăng ký tên miền mới trên Amazon SES

3. Tạo 1 hàm lambda mới

https://github.com/arithmetric/aws-lambda-ses-forwarder

4. Tạo S3 bucket với policy

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GiveSESPermissionToWriteEmail",
            "Effect": "Allow",
            "Principal": {
                "Service": "ses.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::contact-vjai-jp-mailbox/*",
            "Condition": {
                "StringEquals": {
                    "aws:Referer": "AWS-ACCOUNT-ID"
                }
            }
        }
    ]
}
```

