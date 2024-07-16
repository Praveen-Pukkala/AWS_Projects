## Problem Statement:
**Data Processing Task**: When a file is received in S3, its metadata should be stored in a DynamoDB database using a Lambda function.

## Architecture Diagram:
![image](https://github.com/user-attachments/assets/581e2891-9948-40b7-b24c-988472fc3381)



## Implementation in AWS Console:

### Create an S3 Bucket:
- **Name**: `mys3bucket20022024`
- Leave other settings as default.

### Create a Lambda Function:
- **Name**: `your_lambda_function_name`
- **Runtime**: Python 3.8
- Click on "Create Function"
- Paste the following code and click "Deploy":

```python
import boto3
from uuid import uuid4

def lambda_handler(event, context):
    s3 = boto3.client("s3")
    dynamodb = boto3.resource('dynamodb')
    for record in event['Records']:
        bucket_name = record['s3']['bucket']['name']
        object_key = record['s3']['object']['key']
        size = record['s3']['object'].get('size', -1)
        event_name = record['eventName']
        event_time = record['eventTime']
        dynamoTable = dynamodb.Table('newtable')
        dynamoTable.put_item(
            Item={
                'unique': str(uuid4()), 
                'Bucket': bucket_name, 
                'Object': object_key,
                'Size': size, 
                'Event': event_name, 
                'EventTime': event_time
            }
        )
```

Lambda will automatically send logs to CloudWatch and create an execution role.
### Add S3 Trigger to Lambda:
- Select S3 as the trigger source.
- Choose the bucket `mys3bucket20022024`.
- Click “Add”.
### Create a DynamoDB Table:
- Table Name: newtable
- Partition Key: unique
- Adjust the code if you change the table name or partition key.
- Attach DynamoDB Policy to Lambda Execution Role:
- Ensure the Lambda execution role has the necessary permissions to access DynamoDB.
### Testing:
- Upload files to the S3 bucket.
- Check the DynamoDB table for the metadata entries by exploring items and running a scan.
### Output:
![image](https://github.com/user-attachments/assets/9fb85a11-efaa-45ce-99a0-4c33e4f00def)

