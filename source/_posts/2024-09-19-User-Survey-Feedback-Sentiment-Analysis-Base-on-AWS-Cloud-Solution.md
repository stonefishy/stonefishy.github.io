---
title: User Survey Feedback Sentiment Analysis Base on AWS Cloud Solution
date: 2024-09-19 14:00:33
categories: Cloud
tags: [AWS, Machine Learning, Sentiment Analysis, Python]
---

## Introduction
The App Product User Survey Feedback Sentiment Analysis Solution is a cloud-based solution that uses AWS services to analyze user feedback and sentiment of the app product. The solution uses Amazon Comprehend to perform sentiment analysis on the feedback and Amazon S3 to store the data. The solution is designed to be scalable and cost-effective, and can be easily integrated into any app product.

Basically, our survey feedback file is Excel file that contains the user feedback of app and related application info such as OS verion and app version. The feedback text is different language from global users, so we need to translate the text into English using Amazon Translate.  Besides, the feedback file is generated monthly. So, the solution will extract the feedback data from the Excel file, translate the text into English using Amazon Translate, perform sentiment analysis using Amazon Comprehend, and store the data in Amazon S3. The solution will also provide a dashboard to visualize the sentiment analysis results.

The `Amazon Comprehend` is a natural language processing (NLP) service that can analyze text and extract insights such as sentiment, syntax, entities, and key phrases. 

## Solution Architecture
Below is the high-level architecture of the solution:

{% image /assets/images/aws/aws-sentiment-analysis-solution.png, alt=AWS Sentiment Analysis Solution %}

### AWS Services Used
The solution uses the following AWS services:
`AWS S3`: Amazon Simple Storage Service (S3) is a scalable object storage service that can store large amounts of data.
`AWS Lambda`: AWS Lambda is a serverless compute service that can run code without provisioning or managing servers.
`AWS Comprehend`: Amazon Comprehend is a natural language processing (NLP) service that can analyze text and extract insights such as sentiment, syntax, entities, and key phrases.
`AWS SNS`: Amazon Simple Notification Service (SNS) is a messaging service that can be used to send notifications to users.
`AWS Translate`: Amazon Translate is a machine translation service that can translate text from one language to another.
`AWS SQS`: Amazon Simple Queue Service (SQS) is a messaging service that can be used to store and process large amounts of messages.
`AWS CloudWatch`: Amazon CloudWatch is a monitoring service that can be used to monitor the solution and generate metrics.
`AWS Glue`: Amazon Glue is a serverless ETL (extract, transform, and load) service that can be used to extract data from the survey feedback file and store it in Amazon S3.
`AWS Athena`: Amazon Athena is a serverless data analytics service that can be used to query and analyze data stored in Amazon S3.
`AWS QuickSight`: Amazon QuickSight is a business intelligence (BI) service that can be used to create visualizations and dashboards based on the sentiment analysis results.

### Solution Implementation

The solution implementation is divided into the following steps:

1. Create an Amazon S3 bucket as raw data bucket to store the survey feedback Excel file.
2. Uploaded a survey feedback Excel file to the S3 bucket to trigger the AWS Lambda function.
3. AWS Lambda to extract the survey feedback data from the Excel file, translate the text into English using Amazon Translate, sentiment analysis using Amazon Comprehend, and store the data as Parquet format in another Amazon S3 Bucket.
4. Create an Amazon SNS topic to notify users by email when the Lambda process data failed.
5. Create an Amazon CloudWatch to log the lamdba exeuction logs, generate metrics.
6. AWS Glue Crawler to extract the parquet data from the processed amazon S3 bucket and generate a table schema.
7. Using Amazon Athena to query the data from the processed Amazon S3 bucket.
8. Create an Amazon QuickSight dashboard to visualize the sentiment analysis results.

The AWS Lambda core function code is as follows:

```python
import json
import boto3
import pandas as pd
import pyarrow as pa
import pyarrow.parquet as pq
import urllib.parse as urlparse
import io
import os


S3_TARGET_BUCKET = os.environ['QE_SURVEY_PROCESSED_TARGET_BUCKET']
SNS_TOPIC_ARN = os.environ['SNS_TOPIC_ARN']


def translate_feedbacks(feedbacks):
    translate = boto3.client('translate')
    feedbacks_en = []
    print(f"calling AWS Translate to auto detect language and translate feedback, total {feedbacks.__len__()}")

    for feedback in feedbacks:
        response = translate.translate_text(
            Text=feedback,
            SourceLanguageCode='auto',  # Detect source language automatically
            TargetLanguageCode='en'
        )
        feedbacks_en.append(response['TranslatedText'])
        print(f"Finished transalate sourceText: {feedback} to targetText: {response['TranslatedText']}")

    return feedbacks_en


def comprehend_sentiment(feedbacks):
    comprehend = boto3.client('comprehend')
    batch_size = 25
    all_sentiments = []
    print(f"calling AWS Comprehend AI to analysis feedback, total {feedbacks.__len__()}")

    for i in range(0, len(feedbacks), batch_size):
        batch_feedbacks = feedbacks[i:i+batch_size]
        print(f"calling AWS Comprehend AI to analysis feedback, batch {i} - {i+batch_size}")
        comprehend_response = comprehend.batch_detect_sentiment(TextList=batch_feedbacks, LanguageCode='en')
        sentiments = [response['Sentiment'] for response in comprehend_response['ResultList']]
        all_sentiments.extend(sentiments)

    return all_sentiments


def lambda_handler(event, context):
    s3 = boto3.client('s3')
    sns_client = boto3.client('sns')

    source_bucket = event['Records'][0]['s3']['bucket']['name']
    source_key = urlparse.unquote_plus(event['Records'][0]['s3']['object']['key'])
    
    target_bucket = S3_TARGET_BUCKET
    target_key = f"qe-survey/{source_key.replace('.xlsx', '.parquet')}"

    try:
        response = s3.get_object(Bucket=source_bucket, Key=source_key)
        excel_file = response['Body'].read()
        
        columns = ['ce_timestamp', 'ce_host_id', 'ce_host_os', 'ce_hw', 'ce_fw', 'ce_sw', 'survey_feedback', 'survey_rating']
        df = pd.read_excel(io.BytesIO(excel_file), usecols=columns)
        df['ce_timestamp'] = pd.to_datetime(df['ce_timestamp'], format='%Y-%m-%d %H:%M:%S:%f')
        df['survey_feedback'] = df['survey_feedback'].astype(str)
        valid_df = df[df['survey_feedback'].apply(lambda x: x.strip() != '')]
        valid_df = valid_df[valid_df['survey_feedback'] != 'nan']
        
        feedbacks = valid_df['survey_feedback'].tolist()
        
        feedbacks_en = translate_feedbacks(feedbacks)
        valid_df["survey_feedback_en"]= feedbacks_en
        valid_df['sentiment'] = comprehend_sentiment(feedbacks_en)
        
        parquet_buffer = io.BytesIO()
        pq.write_table(pa.Table.from_pandas(valid_df), parquet_buffer)
        parquet_buffer.seek(0)
        
        s3.put_object(Bucket=target_bucket, Key=target_key, Body=parquet_buffer)
        print(f'{source_key} File converted to Parquet {target_key} and stored in S3 bucket {target_bucket} successfully')

        return {
            'statusCode': 200,
            'body': json.dumps(f'{source_key} File converted to Parquet {target_key} and stored in S3 bucket {target_bucket} successfully')
        }
    except Exception as e:
        print(f'Error processing {source_key}: {e}')
        sns_client.publish(
            TopicArn=SNS_TOPIC_ARN,
            Subject='Lambda Function Processing QE Survey Feedback Failure Notification',
            Message=f'An error occurred: {str(e)}'
        )
```

The above code extracts the survey feedback data from the Excel file, translates the text into English using Amazon Translate, performs sentiment analysis using Amazon Comprehend, and stores the data as Parquet format in another Amazon S3 Bucket. also notify users by email when the Lambda process data failed by using Amazon SNS. 

Below is sentiment analysis results visualization using Amazon QuickSight:

{% image /assets/images/aws/aws-sentiment-analysis-result.png, alt=AWS Sentiment Analysis Results %}

This is a high-level overview of the solution implementation. The solution can be further customized and enhanced based on the specific requirements of the app product.


