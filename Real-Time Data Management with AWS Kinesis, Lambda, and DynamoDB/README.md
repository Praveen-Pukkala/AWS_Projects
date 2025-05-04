# 📡 Real-Time Data Management with AWS Kinesis, Lambda, and DynamoDB

A complete real-time data pipeline using AWS services that captures, processes, and stores data for querying. The architecture uses **Kinesis** for data streaming, **Lambda** functions for processing, and **DynamoDB** for storage.

---
![image](https://github.com/user-attachments/assets/5d4000ca-dc9f-4969-a795-8f5f7c53e929)


---

## 🏗️ Architecture Overview

This project sets up the following:

1. **Kinesis Data Stream** for real-time data ingestion.
2. **Producer Lambda Function** to send data to Kinesis.
3. **Consumer Lambda Function** to process and store data.
4. **DynamoDB Table** to store the processed data.
5. **Querying** the stored data using DynamoDB console.

---

## 🔧 Step-by-Step Setup

### ✅ Step 1: Create Kinesis Data Stream

- Go to **AWS Console → Kinesis → Data Streams**
- Click **Create data stream**
- **Name**: `simplilearn-stream`
- **Capacity mode**: On-demand
- Click **Create data stream**

---

### 🐍 Step 2: Create Producer Lambda

1. Navigate to **Lambda → Create function**
   - Name: `simplilearn-producer`
   - Runtime: Python 3.9
   - Architecture: `x86_64`
   - Permissions: Create new role with basic Lambda permissions

2. Attach permissions:
   - `AmazonKinesisFullAccess`
   - `AWSLambdaKinesisExecutionRole`

3. Add the following code (`lambda/producer.py`):

```python
import json
import boto3
import uuid

def lambda_handler(event, context):
    client = boto3.client('kinesis')
    response = client.put_record(
        StreamName='simplilearn-stream',
        Data=json.dumps(event),
        PartitionKey=str(uuid.uuid4())
    )
    print(response)
    return response
```
4.Test with the following JSON:
```
 {
  "id": "b2bfe236-c738-4916-9015-ce47d49d666d",
  "canFly": true,
  "name": "blue jay",
  "colors": ["black", "white", "blue", "grey"]
}
```
## 🧠 Step 3: Create Consumer Lambda

1. Navigate to **Lambda → Create function**
   - **Name**: `simplilearn-consumer`
   - **Runtime**: Python 3.9
   - **Architecture**: x86_64
   - **Permissions**: Create new role with basic Lambda permissions

2. **Attach permissions**:
   - `AmazonKinesisFullAccess`
   - `AWSLambdaKinesisExecutionRole`
   - `AmazonDynamoDBFullAccess`

3. **Add a Kinesis trigger**:
   - **Source**: `simplilearn-stream`

4. **Add the following code** (`lambda/consumer.py`):

```python
import base64
import json
import boto3

def lambda_handler(event, context):
    for record in event['Records']:
        payload = base64.b64decode(record["kinesis"]["data"])
        data = json.loads(payload)
        write_to_db(data)
        print("Data stored in DB:", data)

def write_to_db(data):
    dynamodb = boto3.resource('dynamodb', region_name="us-east-1")
    table = dynamodb.Table("simplilearn-table")
    table.put_item(Item=data)
```
## 🗃️ Step 4: Create DynamoDB Table

1. Navigate to **DynamoDB → Create table**
2. **Table name**: `simplilearn-table`
3. **Partition key**: `id` (type: String)
4. Click **Create table**

---

## 🚀 Step 5: Test the Pipeline

1. **Trigger the Producer Lambda**
   - Update the test payload (e.g., change the `id` or `name`)
   - Click **Test**

2. **Check DynamoDB Table**
   - Go to `simplilearn-table`
   - Click **Explore table items**

3. **Query Data**
   - Use the **Scan** feature with filters  
     (e.g., `name = "blue jay"`)

---

## 🛠️ Troubleshooting

| Issue                  | Solution                                               |
|------------------------|--------------------------------------------------------|
| Data not in DynamoDB   | Check CloudWatch logs for the consumer Lambda          |
| Access denied errors   | Verify IAM permissions                                 |
| Resource not found     | Double-check stream/table names                        |
| Timeout errors         | Increase Lambda timeout (up to 15 minutes)             |

## 🧩 Summary

This AWS-based pipeline:

- Produces data using a Lambda function  
- Streams it via Kinesis  
- Processes it with a consumer Lambda  
- Stores it in DynamoDB for future access

