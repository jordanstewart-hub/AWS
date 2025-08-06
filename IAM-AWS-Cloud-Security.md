

# Cloud Security with AWS IAM
![IAM Cloud Image](https://github.com/user-attachments/assets/4e8ffe0c-cfcc-476a-a1e8-81a64e7bacf3)



**Author:** Jordan Stewart  
**Email:** jordanstewartbusiness@gmail.com


---

## Introducing Today's Project!

Today, I'll be using the AWS IAM service to control who is authenticated (signed in) and authorized (has permissions) in my AWS account. I'll launch an EC2 instance, then control who has access to it by creating some IAM policies and user groups.

### Tools and concepts

In this project I learned to secure AWS using IAM by creating users, groups, and policies, tagging EC2 instances, applying tag-based access controls, and customizing sign-in URLs with account aliases.


---

## Tags

Tags in EC2 are key-value pairs used to organize, manage, and identify resources. They help with cost tracking, automation, and access control by assigning metadata like owner, purpose, or environment to instances.

In this case, I'll creating a tag called "Env" with a value of "production" or "development" to label the instances used in production vs development environments.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_2e0e5a5d)

---

## IAM Policies

IAM (Identity and Access Management) policies are used to control who can do what with your AWS resources. Think of them as permission rules that you attach to users, groups, or roles to define:

- Who can access

- What actions they can perform

- Which resources they can interact with

- When/under what conditions those actions are allowed


IAM policies are written in JSON and are made up of statements that define access permissions. Each statement must include these key elements:

Effect – either "Allow" or "Deny". This decides if the action is permitted or blocked. (Note: "Deny" always overrides "Allow".)

Action – the specific AWS operations (like s3:GetObject or ec2:StartInstances) that the policy covers.

Resource – the ARN (Amazon Resource Name) of the AWS resource(s) the policy applies to.
### Key points on IAM policies:

- IAM policies = rules for AWS access

- Must include: Effect, Action, and Resource

- "Deny" takes precedence over "Allow"

- Policies are written in JSON and attached to users, groups, or roles

---

## My JSON Policy

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_1c864649)

---

## Account Alias

An Account Alias is a friendly name for your AWS account that you can use instead of your account ID to sign in to the AWS Management Console. You would create one to make it easier to remember and share your AWS console's login URL with others.

This setup took less than 2 minutes. Very easy and simple to put together. 

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_0eb4439b)

---

## IAM Users and User Groups

### Users

IAM users are the people that will get access to your resources/AWS account, whereas user groups are the collections/folders of users for easier user management.

### User Groups

An IAM user group is a collection/folder of IAM users. It allows you to manage permissions for all the users in your group at the same time by attaching policies to the group rather than individual users.

Attaching a policy to a user group grants all users in that group the permissions defined in the policy, making it easier to manage access for multiple users at once.

---

## Logging in as an IAM User

You can share a new user's sign-in details by emailing their credentials or downloading and securely sharing the .csv file with their username, password, and login URL.

💡 As a new user, the AWS console will treat you as someone that is starting from 0 again. This is awesome for new team members or new employees. Some of the dashboard panels are showing "Access denied" already.



![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_6f2ab446)

---

## Testing IAM Policies

The action I performed on both of my EC2 instances:  "Stop" the instance state on both. 

### Stopping the production instance

At the top of the page, an angry-looking banner tells us we've failed to stop this instance. The banner tells us it's because we're not authorized! We don't have permission to stop any instance with the production tag.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_0e7a9d6a)

---

## Testing IAM Policies

### Stopping the development instance

The development instance was successfully stopped while the production instance wasn't stopped due to the restricted access on the alias account. 

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-iam_1811801c)



---

---
