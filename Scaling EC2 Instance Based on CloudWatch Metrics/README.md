# Scaling EC2 Instance Based on CloudWatch Metrics
<img width="471" alt="image" src="https://github.com/user-attachments/assets/47320086-0179-4a74-a433-a7dcb13e2836">

### Description:
Let’s take the case of Hotstar — a platform that provides on-demand video streaming services. 
The more the users join the streaming service platform, the more the resources in terms of servers (EC2 in AWS) Hotstar needs to invest in. 
This way, the load is distributed across different servers and leads to jitter-free experience for the customers while watching the videos. 

Another example is Amazon Prime Day, where a bevy of customers access the amazon.com site. Depending on the number of customers logging into the amazon.com site, 
Amazon would like to add more servers for better customer experience.
Both the above actions lead to increased customer satisfaction, which will eventually boost profits for the companies.
This feature of adding and removing servers is called Dynamic Scaling and is a unique feature of the Cloud. 
Simply put, the users of the Cloud can scale to thousands of servers and scale down when appropriate and pay for what they use.
However, that flexibility to add/subtract servers does not come with the on-premises servers, which is why the cost is always fixed. Also, during the slack time, many resources remain under- utilized which is a wastage of CAPEX(capital Expenditure).  One way of adding and deleting the EC2 instances is to do it manually which may lead to extra manual effort, increase in costs, and inaccurate results.
Another approach is to use Auto Scaling to manage the EC2 instances automatically. As Auto Scaling adds more EC2 instances, the software/application installation and configuration can be automated using the AMI (Amazon Machine Images). 
In the previous use case, we have seen how to capture custom metrics (number of users logged in) in the CloudWatch. Here, we would need the same metric to manage (add/delete) the EC2 instances depending on the number of users logged into the website.
## Tools required: AWS Services - CloudWatch, Auto Scaling, EC2
### Expected Deliverables:
•	Use Auto Scaling to manage the EC2 instances.
•	Use EC2 instance and capture the metrics in the CloudWatch.



## Prerequisites
- An AWS account with appropriate permissions.
- Familiarity with the AWS Management Console.
- Basic understanding of EC2, Auto Scaling Groups (ASG), and CloudWatch.

## Steps

### 1. Launch an Ubuntu EC2 Instance
- Go to the EC2 service in the AWS Management Console.
- Click on "Launch Instance."
- Choose "Ubuntu Server 20.04 LTS (Focal Fossa)" as the AMI.
- Select an appropriate instance type like "t2.micro" for demonstration purposes (consider cost and performance for real-world scenarios).
- Configure a security group that allows SSH (port 22) and HTTP (port 80) traffic inbound.
- Launch the instance.

### 2. Install Apache and Create a Webpage (on the launched EC2)
- Connect to the instance via SSH.
- Update package lists:
  ``` sudo apt update```
- Install Apache:
``` sudo apt install apache2```
- Start the Apache service:
``` sudo systemctl start apache2```
- Move to the default web folder:
``` cd /var/www/html```
- Create a new index.html file with content:
  ```echo "World's No. 1 Online Application" > index.html```

- Verify the webpage by accessing the instance's public IP address in a web browser.

### 3. Create an AMI from the Instance
- In the EC2 console, navigate to the instance you launched.
- Under "Actions," select "Image" and then "Create Image."
- Provide a descriptive name for the AMI that includes the date (e.g., "UbuntuWebServer-2024-04-10").
- Click "Create image" to initiate the AMI creation process.

### 4. Create a Launch Template
- Go to the "Launch Templates" section in the EC2 service.
- Click "Create launch template."
- Specify a name like "MyUbuntuTemplateForASG" with a description.
- Choose the AMI you created in step 3.
- Select the same instance type (`t2.micro`) and key pair used for the initial launch.
- Associate the security group created in step 1.
- Under "Advanced details," enable "Detailed Monitoring" to send metrics to CloudWatch every minute.
- Review and create the launch template.

### 5. Create an Auto Scaling Group (ASG)
- In the EC2 service, navigate to "Auto Scaling Groups" and click "Create Auto Scaling group."
- Provide a name like "MyEcommerceAppASG."
- Select the launch template created in step 4.
- Choose a VPC and subnet where the instances will be launched.
- Set the desired capacity, minimum capacity, and maximum capacity to 1, 1, and 4, respectively. This allows the ASG to scale between 1 and 4 instances based on demand.
- Configure health checks to ensure launched instances are healthy.
- Review and create the ASG.
### 6.	Create a CloudWatch Alarm for Scaling:
- Go to the CloudWatch service and navigate to "Alarms."
- Click "Create alarm."
- Select "EC2 Auto Scaling" as the namespace and choose your ASG name.
- Choose the metric "CPUUtilization" and set the statistic to "Average."
- Set the comparison operator to ">" (greater than) and the threshold value to a suitable level based on your application's needs (e.g., 70%).
- Configure the alarm period (e.g., 5 minutes) for when the threshold is breached.
- Under "Actions," choose "Auto Scaling group" and select your ASG name.
- In the "Action" section, specify "Scale Out" to add one instance when the alarm is triggered.
- Configure additional notification settings if desired.
- Review and create the alarm.
### 7.	Test Scaling:
- Use a load testing tool or simulate user traffic to increase the CPU utilization on the instance.
- If the CPU utilization exceeds the threshold for a sustained period (as defined in the alarm), the ASG should automatically launch a new instance to handle the load.


