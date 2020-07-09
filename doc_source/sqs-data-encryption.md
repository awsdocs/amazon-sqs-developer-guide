# Data encryption<a name="sqs-data-encryption"></a>

Data protection refers to protecting data while in\-transit \(as it travels to and from Amazon SQS\) and at rest \(while it is stored on disks in Amazon SQS data centers\)\. You can protect data in transit using Secure Sockets Layer \(SSL\) or client\-side encryption\. You can protect data at rest by requesting Amazon SQS to encrypt your messages before saving them to disk in its data centers and then decrypt them when the messages are received\.

**Topics**
+ [Encryption at rest](sqs-server-side-encryption.md)
+ [Key management](sqs-key-management.md)