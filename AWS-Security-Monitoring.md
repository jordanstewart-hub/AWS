(https://github.com/user-attachments/files/20635032/securitymonitorAWS.md)[Uploading securitym<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System


**Author:** Jordan Stewart  
**Email:** jordanstewartbusiness@gmail.com

---

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

Ever wondered how to keep tabs on who’s accessing your most sensitive data in AWS? Whether it’s API keys, database credentials, or other critical secrets, it's a security risk every time someone accesses your confidential information. That's why companies invest heavily in robust monitoring systems to track and alert on any unusual activity. During this project, I learned how to monitor access to sensitive data like API keys and credentials in AWS. I'll set up AWS CloudTrail to track secret access, use CloudWatch for logging and alerting, and configure SNS to get notifications when secrets are accessed. I will also build a second alert system to compare effectiveness. This hands-on experience is valuable for roles like Security Engineer, DevOps Engineer, and Cloud Administrator. By the end of this project, I will have shown how to securely store secrets, track who's accessing them, and get notified instantly when something suspicious happens.

### Tools and concepts

In this project I learned how to track and respond to sensitive activity in AWS. Key concepts include setting up CloudTrail to log API events, using CloudWatch to monitor logs, and creating SNS notifications for alerts. I built two alert systems to compare effectiveness, gaining hands-on experience in detecting and responding to unauthorized access or unusual behavior. This project builds essential skills for cloud security and monitoring.

### Project reflection

This project in total took me 1.5 hours to complete.

---

## Create a Secret

AWS Secrets Manager helps you protect secrets, which are passwords, API keys, credentials and sensitive information. Instead of storing important credentials in your code (yikes!) or sharing them via email (double yikes!), you can tuck them safely away in Secrets Manager.
In our project, we're just storing a dummy secret, but in real life, this is where you'd keep database passwords, API keys, and other sensitive information that would cause a major headache if they leaked.

To set up for my project, I created a secret called TopSecretInfo that contains the message "Float like a butterfly"



![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

💡 What is AWS CloudTrail?
AWS CloudTrail is a monitoring service - think of it as an activity recorder throughout your AWS account. It documents every action taken, like who did what, when they did it, and where they did it from.

This continuous recording is super valuable for security (spotting unusual activity), troubleshooting (figuring out what changed when something breaks), and meeting compliance requirements (proving you're following the rules). In this project, I'm using it to keep an eye on who's accessing our secret.
💡 What is a trail?
A trail tells CloudTrail exactly what activity to record and where to save those recordings. When you create a trail, you're essentially saying "Hey CloudTrail, please keep track of all xyz activities and store the data in this specific location."



AWS CloudTrail records three main types of events:

Management Events – Also called control plane operations, these log actions like creating or deleting resources (e.g., launching EC2, updating IAM policies).

Data Events – Also known as data plane operations, these capture access to the content within resources (e.g., S3 object-level operations, Lambda function invocations). These are not logged by default.

Insight Events – These detect unusual activity in write management operations, helping identify potential security threats or misconfigurations.

Each API call captured includes details like who made the call, when, from where (IP), and what the response was. These logs are essential for security auditing, operational troubleshooting, and compliance monitoring.

### Read vs Write Activity

💡 What are Read vs Write activity?

Read API activity happens when someone views but doesn't change anything. For example, listing your S3 buckets, describing your EC2 instances, or in our project, viewing (but not changing) metadata about a secret.

Write API activity occurs when changes happen - creating, deleting, modifying resources, or even retrieving the value of a secret (which is what we want to monitor).

---

## Verifying CloudTrail

The first way I retrieved the secret was through the console. When you click "Retrieve secret value" in the console, you're opening the actual content of your secret. Behind the scenes, this triggers what's called a GetSecretValue API call to AWS. It's this specific API call that we want to monitor with CloudTrail, because it represents someone reading your secret!

The second way that I accessed the secret was via AWS CLI. AWS CloudShell gives you a command-line terminal that's already logged into your AWS account and ready to go. This saves you the time you'd usually have to spend on the AWS CLI on your computer, remembering to configure credentials, and keeping everything updated. The command I used was: aws secretsmanager get-secret-value --secret-id "TopSecretInfo" --region us-east-1

I used "Event source" to filter for only Secrets Manager events, which helps me quickly spot if and when the secret was accessed without wading through tons of unrelated events! I then saw events related to "secretsmanager.amazonaws.com". Then I checked for an event called GetSecretValue and there it was logged. This means the secret's value was retrieved or used.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

💡 What are Amazon CloudWatch Logs?
Amazon CloudWatch Logs is a service that helps you bring together your logs from different AWS services, including CloudTrail, for visibility, troubleshooting, and analysis.

It's especially powerful because once your logs are in CloudWatch, you can create alerts based on specific patterns (such as someone accessing your secret), visualize trends, or trigger automated responses.

For this project, we're sending our CloudTrail logs to CloudWatch Logs so we can set up alerts when someone accesses the secret we created.

The key difference between CloudTrail vs CloudWatch is purpose and usage:

CloudTrail- is for recording API activity across your AWS account. It tracks who did what and when—great for audits, security reviews, and compliance. It logs things like user logins, resource creation, and permission changes.

CloudWatch- Logs are for monitoring application and system performance. You send logs (like server errors, app output, or Lambda execution info) to CloudWatch to help detect and troubleshoot issues in real time.

In short:
CloudTrail = tracks actions in AWS
CloudWatch = monitors performance and behavior of systems and apps

They often work together—for example, CloudTrail can send its logs to CloudWatch for alerting or deeper analysis.

💡 What are metric and default values?

Metric value is what gets recorded when our filter spots a match in the logs. I'm setting it to 1 so that each time someone accesses the secret, the counter increases by exactly one.

Default value is what gets recorded when our filter doesn't find any matches during a given time period. I'm setting it to 0 so that time periods with no secret access show up as zero on the charts, rather than not showing up at all. This gives me a complete picture - I can see both when access happened AND when it didn't.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

💡 What are alarm thresholds?
The alarm threshold is when the alarm should trigger. I'm setting up a static threshold so that the alarm goes off when the SecretIsAccessed metric is greater than or equal to 1 in a 5-minute period.

This is a very sensitive setting (i.e. it's easy for this alarm to go off) - which is great for high-security scenarios! In a real production environment you might adjust this depending on how sensitive your secret is. For instance, if it's normal for your application to access a secret 10 times every 5 minutes, you might set a higher threshold to only alert on unusual access patterns.

💡 What is an SNS topic?
An SNS (Simple Notification Service) topic is like a broadcast channel for your notifications. First, you create the channel (topic), then you invite subscribers (such as your email), and finally, you send messages to the topic. SNS automatically delivers that message to all subscribers.

When you set up your email with SNS, AWS doesn't just start sending you emails right away - that would be a bit intrusive! Instead, they want to make sure it's really you asking for these alerts. That's why they've sent a confirmation email to my inbox.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

No email was received this time during this test. Looks like there is some troubleshooting to do.

There were 5 possible troubleshooting errors that I looked into: 
- CloudTrail didn't record the GetSecretValue event.

- CloudTrail isn't sending logs to CloudWatch.

- CloudWatch's metric filter isn't filtering logs correctly.

- CloudWatch's Alarm isn't triggering an action.

- SNS isn't delivering emails to me.

I initially didn't receive because the CloudWatch alarm wasn't configured to the correct alarm state. 

---

## Success!

To validate that my monitoring system worked correctly I adjusted my alarm state settings correctly to notify me accordingly. As previously shown my system did in fact notify me via email that my secret was being accessed. 

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

I updated my CloudTrail configurations because when you enable SNS notification delivery in CloudTrail, it will send a notification to your SNS topic every time a new log file is delivered to your S3 bucket. This is different from a CloudWatch Alarm, which only triggers when a specific pattern (like accessing your secret) is detected in the logs.

The huge wave of emails rushed in and will continue to happen as long as you keep your trail's SNS notification setting on, and there is any activity in your account.

Recap: CloudTrail typically batches events over a period (usually 5-15 minutes) and creates a new log file each time, triggering an SNS notification for each file. Since CloudTrail captures all kinds of management events happening in your AWS account - not just secret access - you end up with multiple log files and therefore multiple notifications.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-monitoring_d7e8f9g0)

---

---
onitorAWS.md…]()
