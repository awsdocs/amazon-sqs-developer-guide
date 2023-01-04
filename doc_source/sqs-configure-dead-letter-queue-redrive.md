# Configuring a dead\-letter queue redrive \(console\)<a name="sqs-configure-dead-letter-queue-redrive"></a>

You can configure a *dead\-letter queue redrive* to move standard unconsumed messages out of an existing dead\-letter queue back to their source queues\. For more information about dead letter queue redrive, see [Moving messages out of a dead\-letter queue](sqs-dead-letter-queues.md#sqs-dead-letter-queues-redrive)\.

**To configure a dead\-letter queue redrive for an existing standard queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. Choose the name of queue that you have configured as a [dead\-letter queue](sqs-configure-dead-letter-queue.md)\.

1. Choose **Start DLQ redrive**\.

1. Under **Redrive configuration**, for **Message destination**, do either of the following:
   + To redrive messages to their source queue, choose **Redrive to source queue\(s\)**\.
   + To redrive messages to another queue, choose **Redrive to custom destination**\. Then, enter the Amazon Resource Name \(ARN\) of an existing destination queue\.

1. Under **Velocity control settings**, choose one of the following:
   + **System optimized** \- Redrive dead\-letter queue messages at the maximum number of messages per second\.
   + **Custom max velocity** \- Redrive dead\-letter queue messages with a custom maximum rate of messages per second\. The maximum allowed rate is 500 messages per second\.
     + It is recommended to start with a small value for "Custom Max Velocity" and verify that the source queue does not get overwhelmed with messages. From there gradually ramp up the "Custom Max Velocity" value, continuing to monitor the state of the source queue.

1. When you finish configuring the dead\-letter queue redrive, choose **Redrive messages**\.
**Important**  
Amazon SQS supports dead\-letter queue redrive only for standard queues in the Amazon SQS console\.  
Amazon SQS doesn't support filtering and modifying messages while redriving them from the dead\-letter queue\.  
A dead\-letter queue redrive task can run a maximum of 36 hours\. Amazon SQS supports a maximum of 100 active redrive tasks per account\.  
The redrive task resets the retention period\. A new `messageID` and `enqueueTime` are assigned to redriven messages\.

1. If you want to cancel the message redrive task, on the **Details** page for your queue, choose **Cancel DLQ redrive**\. When canceling an in progress message redrive, any messages that have already been successfully moved to their move destination queue will remain in the destination queue\.

## Configuring queue permissions for dead\-letter queue redrive<a name="sqs-configure-dead-letter-queue-redrive-permissions"></a>

You can give user access to specific dead\-letter queue actions by adding permissions to your policy\. The minimum required permissions for a dead\-letter queue redrive are as follows:


| Minimum Permissions | Required API methods | 
| --- | --- | 
| To start a message redrive | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-dead-letter-queue-redrive.html) | 
| To cancel an in\-progress message redrive | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-dead-letter-queue-redrive.html) | 
| To show a message move status | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-dead-letter-queue-redrive.html) | 

**To configure permissions for an encrypted queue pair \(a source queue with a dead\-letter queue\)**

Your source queue and its corresponding dead\-letter queue need to share the same [KMS key](sqs-key-management.md)\. Use the following steps to configure minimum permissions for a dead\-letter queue redrive:

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Create a [https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) with the following permissions and attach it to your login IAM [user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) or [role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html):
   + `sqs:ReceiveMessage`
   + `sqs:DeleteMessage`
   + `sqs:GetQueueAttributes`
   + The `Resource` arn of the dead\-letter queue \(e\.g\. "arn:aws:sqs:*<DLQ\_region>*:*<DLQ\_accountId>*:*<DLQ\_name>*"\) 
   + `sqs:SendMessage`
   + The `Resource` arn of the destination queue \(e\.g\. "arn:aws:sqs:*<DestQueue\_region>*:*<DestQueue\_accountId>*:*<DestQueue\_name>*"\)
   + `kms:Decrypt` Allows decryption action
   + `kms:GenerateDataKey`
   + The `Resource` arn of the KMS encryption key \(e\.g\. "arn:aws:kms:*<region>:<accountId>:key/<keyId\_used to encrypt the message body>*"\)

   Your access policy should resemble the following:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "sqs:ReceiveMessage",
           "sqs:DeleteMessage",
           "sqs:GetQueueAttributes"
         ],
         "Resource": "arn:aws:sqs:<DLQ_region>:<DLQ_accountId>:<DLQ_name>"
       },
       {
         "Effect": "Allow",
         "Action": "sqs:SendMessage",
         "Resource": "arn:aws:sqs:<DestQueue_region>:<DestQueue_accountId>:<DestQueue_name>"
       },
       {
         "Effect": "Allow",
         "Action": [
           "kms:Decrypt",
           "kms:GenerateDataKey"
         ],
         "Resource": "arn:aws:kms:<region>:<accountId>:key/<keyId>"
       }
     ]
   }
   ```

**To configure permissions using a non\-encrypted queue pair \(a source queue with a dead\-letter queue\)**

Use the following steps to configure minimum permissions for a standard **unencrypted** dead\-letter queue\. Required minimum permissions are to *receive*, *delete* and *get* attributes from the dead\-letter queue, and *send* attributes to the source queue\. 

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Create a [https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) with the following permissions and attach it to your login IAM [user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) or [role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html):
   + `sqs:ReceiveMessage`
   + `sqs:DeleteMessage`
   + `sqs:GetQueueAttributes`
   + The `Resource` arn of the dead\-letter queue \(e\.g\. "arn:aws:sqs:*<DLQ\_region>*:*<DLQ\_accountId>*:*<DLQ\_name>*"\) 
   + `sqs:SendMessage`
   + The `Resource` arn of the destination queue \(e\.g\. "arn:aws:sqs:*<DestQueue\_region>*:*<DestQueue\_accountId>*:*<DestQueue\_name>*"\)

   Your access policy should resemble the following:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "sqs:ReceiveMessage",
           "sqs:DeleteMessage",
           "sqs:GetQueueAttributes"
         ],
         "Resource": "arn:aws:sqs:<DLQ_region>:<DLQ_accountId>:<DLQ_name>"
       },
       {
         "Effect": "Allow",
         "Action": "sqs:SendMessage",
         "Resource": "arn:aws:sqs:<DestQueue_region>:<DestQueue_accountId>:<DestQueue_name>"
       }
     ]
   }
   ```
