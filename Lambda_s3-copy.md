# AWS S3-to-S3 Automated File Copy using AWS Lambda & Event Notifications

![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-orange?style=flat&logo=awslambda)
![AWS S3](https://img.shields.io/badge/AWS-S3-green?style=flat&logo=amazons3)
![Python](https://img.shields.io/badge/Python-3.12-blue?style=flat&logo=python)
![Boto3](https://img.shields.io/badge/SDK-Boto3-yellow?style=flat)

This project demonstrates an automated event-driven architecture on AWS. Whenever a file is uploaded to a primary S3 bucket (Source), an **S3 Event Notification** automatically triggers an **AWS Lambda function**, which executes a Python script using **Boto3** to copy the newly created object to a secondary S3 bucket (Destination).

---

## 🏗️ Architecture Diagram Overview

```text
[ User / Application ]
          │
          ▼ (Uploads File)
┌────────────────────────────────────────┐
│   Source S3 Bucket                     │
│   (my-source-bucket-lab1)              │
└──────────────────┬─────────────────────┘
                   │
                   │ (s3:ObjectCreated Notification)
                   ▼
┌────────────────────────────────────────┐
│   AWS Lambda Function                  │ ◄── [ IAM Execution Role ]
│   (S3FileCopyFunction)                 │     (GetObject & PutObject)
└──────────────────┬─────────────────────┘
                   │
                   │ (boto3: copy_object)
                   ▼
┌────────────────────────────────────────┐
│   Destination S3 Bucket                │
│   (my-destination-bucket-lab2)         │
└────────────────────────────────────────┘
```
---

## 🚀 Key Features

* **Event-Driven Execution:** Zero manual intervention; copying triggers instantly upon file upload.
* **Serverless Cost-Efficiency:** Lambda only runs when an event occurs, eliminating idle server costs.
* **Granular Access Control:** Uses AWS IAM roles and policies with least-privilege principles (`s3:GetObject` and `s3:PutObject`).
* **Custom Processing Ready:** Unlike standard S3 Replication, Lambda allows adding custom transformations, file renames, or conditional logic before copying.

---

## 1 🛠️ Prerequisites & Setup Steps

### 1. Create S3 Buckets
* **Source Bucket:** `my-source-bucket-lab1`
* **Destination Bucket:** `my-destination-bucket-lab2`

### 2. Configure IAM Policy & Role
* **IAM Console -> Policies -> Create policy par click karein.**
Attach an inline policy to the Lambda execution role to grant necessary S3 permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::my-source-bucket-lab1/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::my-destination-bucket-lab2/*"
        }
    ]
}
```
---
<img width="1274" height="367" alt="image" src="https://github.com/user-attachments/assets/24fa4e97-dfa2-477c-9bf7-df16d2cbee3d" />


## 3. Deploy AWS Lambda Code
```bash
import boto3
import urllib.parse

s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Extract source bucket name and object key from event trigger
    source_bucket = event['Records'][0]['s3']['bucket']['name']
    object_key = urllib.parse.unquote_plus(
        event['Records'][0]['s3']['object']['key'], 
        encoding='utf-8'
    )
    
    # Target destination bucket
    target_bucket = 'my-destination-bucket-lab2'
    
    copy_source = {'Bucket': source_bucket, 'Key': object_key}
    
    try:
        # Copy object from source to destination bucket
        s3.copy_object(CopySource=copy_source, Bucket=target_bucket, Key=object_key)
        print(f"Successfully copied {object_key} from {source_bucket} to {target_bucket}")
        
        return {
            'statusCode': 200,
            'body': 'File copied successfully!'
        }
    except Exception as e:
        print(f"Error copying object: {str(e)}")
        raise e
```
## 4. Configure S3 Event Trigger in Lambda

1. Open your Lambda function (**S3FileCopyFunction**) in the AWS Console.
2. Under the **Function overview** section, click on **+ Add trigger**.
3. In the trigger configuration:
   * **Select a source:** Choose **S3**.
   * **Bucket:** Select your source bucket (`my-source-bucket-lab1`).
   * **Event type:** Select **All object create events** (`s3:ObjectCreated:*`).
4. Acknowledge the recursive invocation warning checkbox and click **Add**.

<img width="1224" height="406" alt="image" src="https://github.com/user-attachments/assets/77d3d820-9382-4d29-8dfc-206b28666775" />

---

# 🧪 Testing & Verification

* Upload any file (image/document) to my-source-bucket-lab1.

* Check my-destination-bucket-lab2 to confirm the file is automatically copied.

* Check AWS CloudWatch Logs under the Lambda log stream to verify successful execution output.
