---
title: How to Fix AWS S3 Event Replacing Space With '+' Character Sign in Object Key Name
date: 2024-09-10 09:22:34
tags: [Cloud, AWS, AWS S3, AWS Lambda, Python]
categories: Cloud
---

The issue with AWS S3 Event notifications is that it replaces spaces with '+' character sign in the object key name. This can cause issues when trying to access the object in S3. It will occurs `NoSuchKey` error if not handling this issue properly.

## Background
Configured a S3 bucket put event notification to a Lambda function. The Lambda function will be triggered when a new object is uploaded to the S3 bucket. I upload a file named '2023 2nd quarter QE survey raw data.xlsx' into S3 bucket, the Lambda function is triggered, but when I try to access the object in S3, I get `NoSuchKey` error in `AWS CloudWatch Logs`. Debugging shows that the object key name is '2023+2nd+quarter+QE+survey+raw+data.xlsx' instead of '2023 2nd quarter QE survey raw data.xlsx'. It means the S3 event notification is replacing the space with '+' character sign in the object key name.

The detail error is below:

``` log
[ERROR] NoSuchKey: An error occurred (NoSuchKey) when calling the GetObject operation: The specified key does not exist.
```

## Solution
There are two solutions to fix this issue, one is from AWS S3 upload file side, another is from Lambda function side.

### 1. From AWS S3 upload file side:
If you have control over the upload process, ensure that the keys are properly URL-encoded.

``` python
import boto3
import urllib.parse

s3_client = boto3.client('s3')

# Example key with spaces
object_key = "2023 2nd quarter QE survey raw data.xlsx"
encoded_key = urllib.parse.quote(key_with_spaces)

# Upload the object
s3_client.upload_file("/tmp/2023 2nd quarter QE survey raw data.xlsx", "my-bucket-name", encoded_key)

```
This will ensure that the object key name is properly URL-encoded, which will prevent the S3 event notification from replacing spaces with '+' character sign. The `urllib.parse.quote` function will replace spaces with '%20' and '+' with '%2B'.

### 2. From Lambda function side:

To fix this issue, we need to modify the Lambda function to handle the object key name with '+' character sign. We can use the `urllib.parse` package to handle it. The package library provide the function `unquote_plus` to replace '+' with space. Here is the code to handle the object key name with '+' character sign:

``` python
import os
import urllib.parse as urlparse
import boto3

s3 = boto3.client('s3')

def lambda_handler(event, context):
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = event['Records'][0]['s3']['object']['key']
    # Replace '+' with space
    object_key = urlparse.unquote_plus(object_key)
    # Download the object
    s3.download_file(bucket_name, object_key, '/tmp/file.txt')
    # Do something with the downloaded file
    #...
```

In the above code, we first get the bucket name and object key from the S3 event notification. We then use the `urlparse.unquote_plus` function to replace '+' with space in the object key name. Finally, we download the object using the `s3.download_file` function.

## Conclusion
To fix the issue with AWS S3 Event notifications replacing space with '+' character sign in the object key name, we need to handle it properly in the Lambda function. We can use the `urllib.parse` package to handle it.