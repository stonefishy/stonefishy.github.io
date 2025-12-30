---
title: Migrate a legacy application to AWS Cloud
date: 2024-03-13 14:20:44
categories: Cloud Native
tags: [Cloud, IaC, AWS, Pulumi, Python, React]
---

## Background
Recently, we got a requirement from the company to move the application to AWS Cloud. The company has a strong focus on security and compliance, and stack holders also want the application more reliable and scalable. The migration also need to be done as soon as possible.

The application running on a local data center. The application is consists of two parts, frontend is a static website built with React and provide the user interface to user, the backend is a Python Flask application that provide the API to interact with the frontend. The backend server also contains a machine learning model algorithm that is used to process the user's ears photo. 

The application main logic is that the user answer some questions and scan and upload their ears photo to the backend server from the website, the backend server will process the photo and return the suggestion result to user to recommend which headset or earphone is the best fit for them.

## Architecture
After analysis application technologies and architecture, base on the requirements, we did some architecture design. Below is the architecture of the application on AWS Cloud. 

{% image /assets/images/aws/aws-migrate-legacy-app-arch.png, alt=Application Architecture on AWS Cloud %}

The application is hosted on AWS Cloud, major is that the frontend is served by CloudFront, the backend is served by ECS, and the user's ears photo is stored in S3 bucket. The application is using the following AWS services:

#### AWS S3 Bucket
Setup two s3 buckets, one is for storing the user's ears photo, and config the object expires after 90 days. second bucket is for storing the static website files. All s3 buckets are public blocked.
#### AWS VPC
Create a dedicate VPC for the application, and configure the subnets, route tables, and security groups. Two public subnets and two private subnets are used.
#### AWS ECR
Use ECR to store the Docker image of the backend application. The image will be built and pushed to ECR by CI/CD pipeline.
#### AWS ECS
Use ECS to run the backend application as a container in private subnets, and configure the auto scaling group and load balancer. Autoscaling minimum size is 2 and maximum size is 20.
#### AWS ALB
Create a ALB to serve the backend ECS service, and configure the listener rules to forward the traffic to the ECS service. The ALB attached the SSL certificate from ACM.
#### AWS S3 VPC Gateway Endpoint
Use the S3 VPC Gateway Endpoint to access the s3 bucket from the backend ECS container.
#### AWS Internet Gateway
The Internet Gateway to connect the VPC to the internet. Put the ALB on the two public subnets across two AZs 
#### AWS CloudWatch
Use CloudWatch to monitor the application performance, and create alarms to notify the team when the application is not running as expected.
#### AWS SNS
Use SNS to notify the team when the application performance is not good, and the team can take action to improve the application performance.
#### AWS ACM
Use ACM to manage the SSL certificate for the ALB and CloudFront, the certificate is issued by the IT team. The application is served over HTTPS.
#### AWS CloudFront
Use CloudFront to serve the static website files, and cache the files to improve the website loading speed. Config CloudFront to access s3 bucket by OAC. Create a another origin for the ALB.
#### AWS Security Group
Create a security group for the ECS container, and allow the traffic from the ALB to the ECS container. And one more security group for the ALB to allow the traffic only from AWS CloudFront prefix list.
#### AWS IAM
Create an IAM role for the ECS container, and attach the necessary policies to the role to access the s3 bucket, ECR, and CloudWatch.
#### AWS WAF
Use WAF to protect the application from common web exploits and attacks. This is mandatory for the company's security policy. The security team will also review the infrastucture and do the security scan the application. The application won't be deployed to production if the security scan failed.

## IaC with Pulumi
Use Pulumi to manage the AWS resources, and create the infrastructure as code. The code will be checked into the source control, and for the pipeline, we're using Bamboo pipeline as company already using Bamboo for CI/CD. The pipeline will doing below major things.
1. Build the Docker image and push to ECR.
2. Deploy the frontend static website to CloudFront, and invalidate the cache to make the content updated for end user.
3. Update the infrastucture by creating or updating the AWS resources by using pulumi.

## Rationale
The migration of the legacy application to AWS Cloud is a complex task, and we need to follow the best practices to make the migration successful.
1. Using CloudFront and S3 bucket to host the static website and user's ears photo is scalable and cost-effective. 
2. Using the ECS and ALB to serve the backend application is also a good choice to improve the application performance and scalability. We're not using AWS API Gateway and AWS Lambda to serve as backend because we are requested to migrate the application to Cloud as soon as possible. Build the python `Flask` application to a docker image and push to ECR is a good practice to deploy the application to AWS Cloud in this situation.
3. Using the VPC and security group to isolate the application and improve the security is a must. The ECS is located in private subnets, and the ALB is in public subnets, and the traffic is only allowed from AWS CloudFront prefix list to ALB, then forward traffic to ECS container.
4. Using the ACM to manage the SSL certificate for the ALB and CloudFront is a good practice to improve the security and compliance.
5. Using the CloudWatch to monitor the application performance and create alarms to notify the team when the application is not running as expected is a good practice to improve the application reliability.
6. Using the IAM role to access the s3 bucket, ECR, and CloudWatch is a good practice to improve the security and control.
7. Using the WAF to protect the application from common web exploits and attacks is a mandatory requirement for the company's security policy.
8. Using Pulumi to manage the AWS resources as code is a good practice to improve the automation and reliability of the migration process.

## Summary
This is just a sample of how to migrate a legacy application to AWS Cloud, and there are many other factors to consider when migrating a legacy application to AWS Cloud. The key is to follow the best practices and use the right tools to make the migration successful base on the requirements.

