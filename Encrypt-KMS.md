
# Encrypt Data with AWS KMS

**Author:** Jordan Stewart  
**Email:** jordanstewartbusiness@gmail.com

---

![](![Screenshot 2025-07-08 191133](https://github.com/user-attachments/assets/649987ff-4149-4bb3-93c0-deb3b9f2ae51)
)

---

## Introducing Today's Project!

Data security is a big deal - it's how companies and engineers protect sensitive information, customer data, or even national secrets. That's why data breaches are often a top concern of company leaders, and they're costing companies an average of $4.5 million to resolve and recover from. How can you protect your data from unauthorized access, while making sure the right people can see and use it? That’s where encryption comes in. Encryption converts data into a secure format that only authorized users can transform back and read. You use encryption keys, which are like digital codes, to encrypt and decrypt the data.

During this project I am going to:

🔑 Create encryption keys with AWS KMS (Key Management Service).

🗄️ Encrypt a DynamoDB database with a KMS key.

➕ Add and retrieve data from your database to test your encryption.

🕵️‍♀️ Observe how AWS stops unauthorized access to your data.

💎 Give a user encryption access.

### Tools and concepts

During this project I learned key AWS services and concepts related to data protection. These included:

-AWS Key Management Service (KMS) for creating and managing encryption keys

-Customer Master Keys (CMKs) for encrypting and decrypting data securely

-Envelope encryption, which combines KMS with data keys for efficient encryption

-IAM and key policies for managing access control to encryption keys

-Integration of KMS with other services like S3, EC2, or Lambda to secure data at rest and in transit

Overall, the project strengthened my understanding of securing sensitive information using AWS-native encryption solutions.

### Project reflection

45 minutes

YEs

---

## Encryption and KMS

Encryption is the process of converting readable data (plaintext) into an unreadable format (ciphertext) using an algorithm and an encryption key. This ensures that only authorized parties with the correct key can access or decrypt the information, helping protect data confidentiality and security.










💡 What is AWS KMS?
AWS Key Management Service (KMS) is a secure vault for your encryption keys. You use KMS to create, manage, and use encryption keys that protect the data in your AWS resources.

💡 What is symmetric encryption?
Symmetric encryption use a single encryption key to both lock (encrypt) and unlock (decrypt) your data. They are generally faster and more efficient for encrypting large amounts of data, which is why I'm using one for our DynamoDB table. 

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-kms_a2b3c4d5)

---

## Encrypting Data

Amazon DynamoDB is a fully managed NoSQL database service offered by AWS that provides fast and predictable performance at any scale. It is designed for high availability and can handle key-value and document data structures, making it ideal for applications that require low-latency access to large volumes of data. DynamoDB automatically handles data replication, scaling, and backups.

💡 What are the different encryption options in DynamoDB?
DynamoDB offers a few different encryption options:

Owned by Amazon DynamoDB: Amazon DynamoDB fully manages the key, so you have no access or visibility to the key. Great for basic encryption where you don't need any control.

AWS managed key: AWS Key Management Service (KMS) manages the key, so it's not a customer managed key like what we created! You can see the key and its usage, but management is done by AWS.

Stored in your account, and owned and managed by you: aka a customer managed key (CMK). You create and manage the key in KMS, giving you full control. This is the most secure option and the one we're using in this project.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-kms_q8r9s0t1)

---

## Data Visibility

A KMS key manages user permissions using AWS Identity and Access Management (IAM) policies and Key policies. Key policies are attached directly to the KMS key and define who can use or manage it. IAM policies can also grant users permission to use the key, but the key policy must allow access as well. Both policies work together to control access to cryptographic operations like encrypting and decrypting data.

💡 Why can I see the item?
Even though the data is encrypted, you as a user have permissions to use the encryption key in KMS.

DynamoDB is designed to decrypt the data on your behalf. When data is requested by an authorized user (like you) or an authorized application, DynamoDB retrieves the encrypted data, decrypts it with the key, then shows you the decrypted format so you can use it instantly. This security feature is called transparent data encryption.

Transparent data encryption makes sure that your data is secure at rest, yet still accessible to authorized users that have the right permissions.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-kms_c0d1e2f3)

---

## Denying Access

These are the steps I took to set up an IAM User: 

-Open the IAM console.

-Select Users in the left navigation pane.

-Select Create user.

-Enter a username, such as "project-kms-user".

-Check Provide user access to the AWS Management Console - optional. This will let us log in as the test user and see the KMS key's effect later.

-Uncheck Users must create a new password at next sign-in,  to save ourselves time when we log in as the test user.

-Select Next.

-Select Attach existing policies directly.

-Add AmazonDynamoDBFullAccess as a permission policy.

-Select Next.

-Select Create user.

-Make sure to select Download .csv file to save a backup of the user's login details. You won't get this option again once you click out of this page




When I checked my DynamoDB table I received a messaged saying "access denied". 
💡 Why is my access denied?
The new IAM user I logged in as (project-kms-user) does not have the permission to decrypt the data.

Since the DynamoDB table is encrypted with a specific AWS KMS key, and your user does not have permission to use this key, the system prevents access for data security.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-kms_w0x1y2z3)

---

## EXTRA: Granting Access

These are the steps I took to grant the test user permission to use the key:

-Head back to the window with IAM Admin User logged in.

-Head back to the Key Management Service console.

-Modify the Key Policy

-In the KMS dashboard, select Customer-managed keys from the left hand sidebar.

-Select project-kms-key.

-Scroll down the Key policy tab, and select Add next to Key users.

-Add project-kms-user as a test user of the key.

-Select Add.

-Scroll back up to the Key policy section and select Switch to policy view

-Scroll down until you can see the policy section for "Allow use of the key" and in that section the user "project-kms-user" was listed.



I logged in with the test user's credentials after adding permisson to use the key for the test user via admin account. Once I was logged in with the test user I was then allowed to access the database's items.

💡 What's the difference between encryption and other ways to control access?

Other access controls, like security groups or permission policies, control access to a resource (e.g. a DynamoDB table) or an entire service (e.g. DynamoDB). However, these don't protect the actual data within the resource, like the items inside a DynamoDB table. Once someone has access, they can see all the data they’re allowed to. These controls don’t prevent someone with access from reading or misusing the data. If you use KMS encryption, you secure the data within the resource. Even if someone bypasses your access controls (like hacking into a server or intercepting data), they can’t understand the data unless they have the decryption key. This adds a layer of security - only users with access permissions and decryption keys can view the actual data.

![Image](http://learn.nextwork.org/authentic_azure_zealous_melon/uploads/aws-security-kms_feffb2fb8)

---

---
s-encrypt-project.md…]()
