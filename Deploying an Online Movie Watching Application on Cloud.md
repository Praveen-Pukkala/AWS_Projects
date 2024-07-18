# Deploying an Online Movie Watching Application on Cloud (SAAS)

## Description

As part of your role at Binge Watch Online, an online entertainment provider company, you've been tasked with deploying the company's website on the cloud. Users have reported slow page loading speeds after the initial deployment. The website receives global traffic, and its static assets (such as pages) are currently served from a single server. Your objective is to improve performance by implementing efficient load balancing at the DNS level.

## Problem Statement

- **Company:** Binge Watch Online
- **Task:** Deploy the website on a cloud platform ( AWS).
- **Challenge:** Optimize page loading speeds for global users.
- **Required Tools (AWS):**
  - **Route 53:** Manage DNS and global traffic routing. (optional)
  - **S3 Bucket:** Store static assets (e.g., images, videos, CSS files).
  - **CloudFront:** Set up a Content Delivery Network (CDN) for improved read performance.

## Solution Approach
Entertainment IN SAAS Deployment (W/o using Infrastructure provisioning):
## Architecture Diagram
![image](https://github.com/user-attachments/assets/b4d118cf-ddad-49b2-a829-5c7de3bc178a)


- Create an S3 bucket and upload entertainment app files to the bucket.
- Enable static website hosting in the bucket.
- Unblock public access for the bucket and add a bucket policy to allow GET requests.
```{
  "Id": "Policy1703229669565",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1703229641014",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::vd-entainment-capstone1/*",
      "Principal": "*"
    }
  ]
}
```
- Ensure the website is accessible using the static DNS of the S3 bucket.
- Create CDN and attach S3 as the source
- If you notice the newly generated policy by CloudFront for the S3 bucket, you’ll need to add it to the S3 bucket’s permissions. 
  To achieve this, remove the older policy. Our goal is to allow access to the S3 bucket only from the CloudFront distribution, eliminating public access to the bucket.
```
{
        "Version": "2008-10-17",
        "Id": "PolicyForCloudFrontPrivateContent",
        "Statement": [
            {
                "Sid": "AllowCloudFrontServicePrincipal",
                "Effect": "Allow",
                "Principal": {
                    "Service": "cloudfront.amazonaws.com"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::vd-entainment-capstone1/*",
                "Condition": {
                    "StringEquals": {
                      "AWS:SourceArn": "arn:aws:cloudfront::411357065850:distribution/E3MZOBBDTOYUHQ"
                    }
                }
            }
        ]
      }

```
- Create OAI so that CDN can get objects from S3 bucket
- Attach edge locations so that data is distributed across all the servers across the globe
- Access entertainment webpage using CDN, and it will be accessible
- Thus solved the speed issue as S3 scales automatically based on traffic and load is globally balanced using CDN

## Extra approach:
If you want to distribute traffic globally and use a custom DNS name, you can set up Amazon Route 53 as your DNS service. 
By creating a Route 53 hosted zone and attaching your CloudFront distribution to it as an A record, you’ll be able to route requests to your website efficiently. 
This combination ensures reliable and low-latency access for users worldwide.





