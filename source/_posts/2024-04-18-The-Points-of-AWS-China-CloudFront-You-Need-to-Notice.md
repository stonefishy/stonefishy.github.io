---
title: The Points of AWS China CloudFront You Need to Notice
date: 2024-04-18 10:16:54
categories: 云计算
tags: [云计算, AWS]
swiper: true
---

There are much difference between AWS Global and AWS China. The background of this blog is that I'm responsible for migrating the aws global application to aws china. The application already go lived on AWS Global. The application is  collecting the user inforamtion and for business logic. The business wants this application to serve China customer. Due to the application regulation,  the application needs to deployed in AWS China and store the user information in AWS China.

The application is using below AWS services:

- AWS S3: store the static website assets and user information.
- AWS ALB: the load balancer for the application.
- AWS ASG: auto scaling group for the application.
- AWS ECR: store the application container image.
- AWS ECS: run the application container.
- AWS ACM: manage the SSL certificate.
- AWS WAF: web application firewall.
- AWS VPC: virtual private cloud.
- AWS S3 VPC Gateway: access the S3 bucket from the VPC.
- AWS CloudWatch: monitor the application logs, performance and alarms.
- AWS SNS: notificate the stack holder when application performance is abnormal.
- AWS CloudFront: serve the static website and user information.

## AWS China
The AWS China is a separate entity operated by a local partner in compliance with Chinese regulations. Data centers located in Beijing and Ningxia. The operator is different between Beijing and Ningxia. Beijing region operated by Sinnet(光环新网)，Ningxia region operated by NWCD(西云数据). Basically， the service price of Ningxia region is cheaper than Beijing region. You can find the detail pricing in the AWS China link https://calculator.amazonaws.cn/#/. AWS Fargate priciing is here https://www.amazonaws.cn/en/fargate/pricing


## Difference between AWS Global and AWS China
The AWS China has many limiation and difference with AWS Global. And also some new services are not available in AWS China. When you migrate the application to AWS China, you need to consider the below points:

1. AWS China has different pricing policy. The pricing policy is different between Beijing and Ningxia. 
2. The Infrastructure code is different between AWS Global and AWS China. The code need to be modified to adapt to AWS China.
3. The Website should be do the ICP filling and Goverment Filling. (域名备案，网安备案)

### Infrastructure as Code
We're using `Pulumi` to manage the infrastructure as code. Pulumi is a tool for developing, building, and deploying cloud applications and infrastructure. It supports multiple cloud providers including AWS, Azure, GCP, and Kubernetes. 
There are AWS Service Resource definition is different with AWS Global on AWS China. In AWS China there is an `amazonaws.com.cn` string for endpoint, and `aws-cn` ARN prefix. The code need to be modified to adapt to AWS China.

#### AWS China
- AWS EndPoint: xxxxxxx.s3.cn-northwest-1.**amazonaws.com.cn**/example.txt
- AWS ARNs: arn:**aws-cn**:s3:::xxxxxxx/example.txt

#### AWS Global
- AWS EndPoint: xxxxxxx.s3.cn-northwest-1.**amazonaws.com**/example.txt
- AWS ARNs: arn:**aws**:s3:::xxxxxxx/example.txt

### CloudFront
In our application is much difference between AWS Global and AWS China, especially the CloudFront.
- Requires ICP filing and domain name filing in AWS China.
- The CloudFront provides domain name like "*.cloudfront.cn" which cannot be used in for website serving in AWS China. You can not access the website through the CloudFront domain name. It returns 403 Forbidden error.
- The SSL/TLS certificates for CloudFront does not support the Amazon Certificate Manager in AWS China. It requires to use SSL/TLS certificate from third party, and then - - import certificate in IAM. It is only support IAM to store the certificates for CloudFront in AWS China.
- The CloudFront does not supports the Amazon WAF in AWS China.
- The Cache polices and Origin request polices does not support in AWS China
- The Lambda@Edge is not available in AWS China.
- CloudFront origin access only supports legacy access identities OAI for S3 bucket, does not support OAC in AWS China
- The CloudFront origin for S3 bucket which is not a website endpoint, the following format: `bucket-name.s3.region.amazonaws.com.cn`, remember `region` after `s3`
- The CloudFront origin for S3 bucket which is a website endpoint, use the following format: `bucket-name.s3-website.region.amazonaws.com.cn`, remember `region` after `s3-website`

For more information, please refer to the AWS China CloudFront https://docs.amazonaws.cn/en_us/aws/latest/userguide/cloudfront.html#feature-diff


## Summary
In this blog, we have discussed the important points when migrate the aws global application to aws china, especially for the AWS CloudFront. We have listed the difference between AWS Global and AWS China, and also the CloudFront difference between AWS Global and AWS China.

If you want to know more about AWS China service difference with AWS Global, you can refer to this official link https://docs.amazonaws.cn/en_us/aws/latest/userguide/services.html

Hope this blog can help you to migrate the application to AWS China.

## Useful Links
- AWS China Service Difference: https://docs.amazonaws.cn/en_us/aws/latest/userguide/services.html
- AWS China Service Pricing: https://calculator.amazonaws.cn/#/
- AWS China Fargate Pricing: https://www.amazonaws.cn/en/fargate/pricing
- AWS China Edge Location: https://www.amazonaws.cn/en/cloudfront/features/
- AWS China CloudFront Error Investigation: https://zhuanlan.zhihu.com/p/182517851
- ICP/IP地址/域名信息备案管理系统: https://beian.miit.gov.cn/#/Integrated/index
- 全国互联网安全管理服务平台: https://beian.mps.gov.cn/#/query/webSearch