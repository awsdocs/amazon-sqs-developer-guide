# Protecting Data Using Server\-Side Encryption \(SSE\) and AWS KMS<a name="sqs-server-side-encryption"></a>

SSE lets you transmit sensitive data in encrypted queues\. SSE protects the contents of messages in Amazon SQS queues using keys managed in AWS Key Management Service \(AWS KMS\)\. For information about managing SSE using the AWS Management Console or the AWS SDK for Java \(and the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`, `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`, and `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)` actions\), see the following tutorials:
+ [Tutorial: Configuring Server\-Side Encryption \(SSE\) for an Existing Amazon SQS Queue](sqs-create-queue-sse.md)
+ [Tutorial: Configuring Server\-Side Encryption \(SSE\) for an Existing Amazon SQS Queue](sqs-configure-sse-existing-queue.md)
+ [Example 3: Enable Compatibility between AWS Services Such as Amazon CloudWatch Events, Amazon S3, and Amazon SNS and Queues with SSE](#compatibility-with-aws-services)

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/Mw1NVpJsOZc?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/Mw1NVpJsOZc?rel=0&amp;controls=0&amp;showinfo=0)

SSE encrypts messages as soon as Amazon SQS receives them\. The messages are stored in encrypted form and Amazon SQS decrypts messages only when they are sent to an authorized consumer\.

**Important**  
All requests to queues with SSE enabled must use HTTPS and [Signature Version 4](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\.  
You can't associate an [encrypted queue](#sqs-server-side-encryption) that uses an AWS managed Customer Master Key for Amazon SQS with a Lambda function in a different AWS account\.  
Some features of AWS services that can send notifications to Amazon SQS using the AWS Security Token Service `[AssumeRole](http://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)` action are compatible with SSE but work *only with standard queues:*  
[Auto Scaling Lifecycle Hooks](http://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)
[AWS Lambda Dead\-Letter Queues](http://docs.aws.amazon.com/lambda/latest/dg/dlq.html)
Other features of AWS services or third\-party services that send notifications to Amazon SQS aren't compatible with SSE, despite allowing you to set an encrypted queue as a target:  
[AWS IoT Rule Actions](http://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html)
For information about compatibility of other services with encrypted queues, see [Example 3: Enable Compatibility between AWS Services Such as Amazon CloudWatch Events, Amazon S3, and Amazon SNS and Queues with SSE](#compatibility-with-aws-services) and your service documentation\.

AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. When you use Amazon SQS with AWS KMS, the [data keys](#sqs-sse-key-terms) that encrypt your message data are also encrypted and stored with the data they protect\.

The following are benefits of using AWS KMS:
+ You can create and manage [customer master keys \(CMKs\)](#sqs-sse-key-terms) yourself\.
+ You can also use the AWS managed CMK for Amazon SQS, which is unique for each account and region\.
+ The AWS KMS security standards can help you meet encryption\-related compliance requirements\.

For more information, see [What is AWS Key Management Service?](http://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide* and the [AWS Key Management Service Cryptographic Details](https://d0.awsstatic.com/whitepapers/KMS-Cryptographic-Details.pdf) whitepaper\.

**Topics**
+ [What Does SSE for Amazon SQS Encrypt?](#sqs-encryption-what-does-sse-encrypt)
+ [Key Terms](#sqs-sse-key-terms)
+ [How Does the Data Key Reuse Period Work?](#sqs-how-does-the-data-key-reuse-period-work)
+ [How Do I Estimate My AWS KMS Usage Costs?](#sqs-estimate-kms-usage-costs)
+ [What AWS KMS Permissions Do I Need to Use SSE for Amazon SQS?](#sqs-what-permissions-for-sse)
+ [Errors](#sqs-sse-troubleshooting-errors)

## What Does SSE for Amazon SQS Encrypt?<a name="sqs-encryption-what-does-sse-encrypt"></a>

SSE encrypts the body of a message in an Amazon SQS queue\.

SSE doesn't encrypt the following:
+ Queue metadata \(queue name and attributes\)
+ Message metadata \(message ID, timestamp, and attributes\)
+ Per\-queue metrics

Encrypting a message makes its contents unavailable to unauthorized or anonymous users\. This doesn't affect the normal functioning of Amazon SQS:
+ A message is encrypted only if it is sent after the encryption of a queue is enabled\. Amazon SQS doesn't encrypt backlogged messages\.
+ Any encrypted message remains encrypted even if the encryption of its queue is disabled\.

Moving a message to a [dead\-letter queue](sqs-dead-letter-queues.md) doesn't affect its encryption:
+ When Amazon SQS moves a message from an encrypted source queue to an unencrypted dead\-letter queue, the message remains encrypted\.
+ When Amazon SQS moves a message from a unencrypted source queue to an encrypted dead\-letter queue, the message remains unencrypted\.

## Key Terms<a name="sqs-sse-key-terms"></a>

The following key terms can help you better understand the functionality of SSE\. For detailed descriptions, see the *[Amazon Simple Queue Service API Reference](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Data Key**  
The data encryption key \(DEK\) responsible for encrypting the contents of Amazon SQS messages\.  
For more information, see [Data Keys](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) in the *AWS Key Management Service Developer Guide* and [Envelope Encryption](http://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/how-it-works.html#envelope-encryption) in the *AWS Encryption SDK Developer Guide*\.

**Data Key Reuse Period**  
The length of time, in seconds, for which Amazon SQS can reuse a data key to encrypt or decrypt messages before calling AWS KMS again\. An integer representing seconds, between 60 seconds \(1 minute\) and 86,400 seconds \(24 hours\)\. The default is 300 \(5 minutes\)\. For more information, see [How Does the Data Key Reuse Period Work?](#sqs-how-does-the-data-key-reuse-period-work)\.  
In the unlikely event of being unable to reach AWS KMS, Amazon SQS continues to use the cached data key until a connection is reestablished\.

**Customer Master Key ID**  
The alias, alias ARN, key ID, or key ARN of an AWS managed customer master key \(CMK\) or a custom CMK—in your account or in another account\. While the alias of the AWS managed CMK for Amazon SQS is always `alias/aws/sqs`, the alias of a custom CMK can, for example, be `alias/MyAlias`\. You can use these CMKs to protect the messages in Amazon SQS queues\.   
Keep the following in mind:  
+ If you don't specify a custom CMK, Amazon SQS uses the AWS managed CMK for Amazon SQS\. For instructions on creating custom CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
+ The first time you use the AWS Management Console to specify the AWS managed CMK for Amazon SQS for a queue, AWS KMS creates the AWS managed CMK for Amazon SQS\.
+ Alternatively, the first time you use the `SendMessage` or `SendMessageBatch` action on a queue with SSE enabled, AWS KMS creates the AWS managed CMK for Amazon SQS\.
You can create CMKs, define the policies that control how CMKs can be used, and audit CMK usage using the **Encryption Keys** section of the AWS KMS console or using AWS KMS actions\. For more information about CMKs, see [Customer Master Keys](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) in the *AWS Key Management Service Developer Guide*\. For more examples of CMK identifiers, see [KeyId](http://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html#API_DescribeKey_RequestParameters) in the *AWS Key Management Service API Reference*\.  
There are additional charges for using AWS KMS\. For more information, see [How Do I Estimate My AWS KMS Usage Costs?](#sqs-estimate-kms-usage-costs) and [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing)\.

## How Does the Data Key Reuse Period Work?<a name="sqs-how-does-the-data-key-reuse-period-work"></a>

Amazon SQS uses a single customer master key \(either the AWS managed CMK for Amazon SQS or a custom CMK\) to provide [envelope encryption](http://docs.aws.amazon.com/kms/latest/developerguide/workflow.html#envelope_encryption) and decryption of multiple Amazon SQS messages during the *data key reuse period*\. To make the most of the [data key reuse period](#sqs-sse-key-terms), keep the following in mind:
+ A shorter reuse period provides better security but results in more calls to AWS KMS, which might incur charges beyond the Free Tier\.
+ Although the data key is cached separately for encryption and for decryption, the reuse period applies to both copies of the data key\.
+ [Principals](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) \(AWS accounts or IAM users\) don't share data keys \(messages sent by unique principals always get unique data keys\)\. Thus, the volume of calls to AWS KMS is a multiple of the number of unique principals in use during the data key reuse period:
  + When you send messages using the `SendMessage` or `SendMessageBatch` action, Amazon SQS typically calls the AWS KMS `GenerateDataKey` and `Decrypt` actions once per every data key reuse period\.
**Note**  
For each data key that AWS KMS generates, SSE calls the `Decrypt` action to verify the integrity of the data key before using it\.
  + When you receive messages using the `ReceiveMessage` action, Amazon SQS typically calls the AWS KMS `Decrypt` action once per every data key reuse period\.

## How Do I Estimate My AWS KMS Usage Costs?<a name="sqs-estimate-kms-usage-costs"></a>

To predict costs and better understand your AWS bill, you might want to know how often Amazon SQS uses your customer master key \(CMK\)\.

**Note**  
Although the following formula can give you a very good idea of expected costs, actual costs might be higher because of the distributed nature of Amazon SQS\.

To calculate the number of API requests \(`R`\) *per queue*, use the following formula:

```
R = B / D * (2 * P + C)
```

`B` is the billing period \(in seconds\)\.

`D` is the [data key reuse period](#sqs-sse-key-terms) \(in seconds\)\.

`P` is the number of producing [principals](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) that send to the Amazon SQS queue\.

`C` is the number of consuming principals that receive from the Amazon SQS queue\.

**Important**  
In general, producing principals incur double the cost of consuming principals\. For more information, see [How Does the Data Key Reuse Period Work?](#sqs-how-does-the-data-key-reuse-period-work)  
If the producer and consumer have different IAM users, the cost increases\.

The following are example calculations\. For exact pricing information, see [ AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing/)\.

### Example 1: Calculating the Number of AWS KMS API Calls for 2 Principals and 1 Queue<a name="example-1-queue-2-principals"></a>

This example assumes the following:
+ The billing period is January 1\-31 \(2,678,400 seconds\)\.
+ The data key reuse period is set to 5 minutes \(300 seconds\)\.
+ There is 1 queue\.
+ There is 1 producing principal and 1 consuming principal\.

```
2,678,400 / 300 * (2 * 1 + 1) = 26,784
```

### Example 2: Calculating the Number of AWS KMS API Calls for Multiple Producers and Consumers and 2 Queues<a name="example-2-queues-multiple-principals"></a>

This example assumes the following:
+ The billing period is February 1\-28 \(2,419,200 seconds\)\.
+ The data key reuse period is set to 24 hours \(86,400 seconds\)\.
+ There are 2 queues\.
+ The first queue has 3 producing principals and 1 consuming principal\.
+ The second queue has 5 producing principals and 2 consuming principals\.

```
(2,419,200 / 86,400 * (2 * 3 + 1)) + (2,419,200 / 86,400 * (2 * 5 + 2)) = 532
```

## What AWS KMS Permissions Do I Need to Use SSE for Amazon SQS?<a name="sqs-what-permissions-for-sse"></a>

Before you can use SSE, you must configure AWS KMS key policies to allow encryption of queues and encryption and decryption of messages\. For examples and more information about AWS KMS permissions, see [AWS KMS API Permissions: Actions and Resources Reference](http://docs.aws.amazon.com/kms/latest/developerguide/kms-api-permissions-reference.html) in the *AWS Key Management Service Developer Guide*\.

**Note**  
You can also manage permissions for KMS keys using IAM policies\. For more information, see [Using IAM Policies with AWS KMS](http://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html)\.  
While you can configure global permissions to send to and receive from Amazon SQS, AWS KMS requires explicitly naming the full ARN of CMKs in specific regions in the `Resource` section of an IAM policy\.

You must also ensure that the key policies of the customer master key \(CMK\) allow the necessary permissions\. To do this, name the principals that produce and consume encrypted messages in Amazon SQS as users in the CMK key policy\. 

Alternatively, you can specify the required AWS KMS actions and CMK ARN in an IAM policy assigned to the principals which produce and consume encrypted messages in Amazon SQS\. For more information, see [Managing Access to AWS KMS CMKs](http://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#managing-access) in the *AWS Key Management Service Developer Guide*\.

### Example 1: Allow a User to Send Single or Batched Messages to a Queue with SSE<a name="send-to-encrypted-queue"></a>

The producer must have the `kms:GenerateDataKey` and `kms:Decrypt` permissions for the customer master key \(CMK\)\.

```
{
   "Version": "2012-10-17",
      "Statement": [{
         "Effect": "Allow",
         "Action": [
            "kms:GenerateDataKey",
            "kms:Decrypt"
         ],
         "Resource": "arn:aws:kms:us-east-2:123456789012:key/1234abcd-12ab-34cd-56ef-1234567890ab"
         }, {
         "Effect": "Allow",
         "Action": [
            "sqs:SendMessage",
            "sqs:SendMessageBatch"
         ],
         "Resource": "arn:aws:sqs:*:123456789012:MyQueue"
      }]
}
```

### Example 2: Allow a User to Receive Messages from a Queue with SSE<a name="receive-from-encrypted-queue"></a>

The consumer must have the `kms:Decrypt` permission for any customer master key \(CMK\) that is used to encrypt the messages in the specified queue\. If the queue acts as a [dead\-letter queue](sqs-dead-letter-queues.md), the consumer must also have the `kms:Decrypt` permission for any CMK that is used to encrypt the messages in the source queue\.

```
{
   "Version": "2012-10-17",
      "Statement": [{
         "Effect": "Allow",
         "Action": [
            "kms:Decrypt"
         ],
         "Resource": "arn:aws:kms:us-east-2:123456789012:key/1234abcd-12ab-34cd-56ef-1234567890ab"
         }, {
         "Effect": "Allow",
         "Action": [
            "sqs:ReceiveMessage"
         ],
         "Resource": "arn:aws:sqs:*:123456789012:MyQueue"
      }]
}
```

### Example 3: Enable Compatibility between AWS Services Such as Amazon CloudWatch Events, Amazon S3, and Amazon SNS and Queues with SSE<a name="compatibility-with-aws-services"></a>

To allow Amazon CloudWatch Events, Amazon S3 event notifications, or Amazon SNS topic subscriptions to work with encrypted queues, you must perform the following steps:

1. [Create a customer master key \(CMK\)\.](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-keys-console)

1. To allow the AWS service feature to have the `kms:GenerateDataKey` and `kms:Decrypt` permissions, add the following statement to the policy of the CMK\.
**Note**  
For Amazon CloudWatch Events, use `events`
For Amazon S3 event notifications, use `s3`
For Amazon SNS topic subscriptions, use `sns`

   ```
   {
      "Version": "2012-10-17",
         "Statement": [{
            "Effect": "Allow",
            "Principal": {
               "Service": "service.amazonaws.com"
            },
            "Action": [
               "kms:GenerateDataKey*",
               "kms:Decrypt"
            ],
            "Resource": "*"
          }]
   }
   ```

1. [Create a new SSE queue](sqs-create-queue-sse.md) or [configure an existing SSE queue](sqs-configure-sse-existing-queue.md) using the ARN of your CMK\.

**Learn More**
+ [Subscribe to a Topic](http://docs.aws.amazon.com/sns/latest/dg/SubscribeTopic.html) in the *Amazon Simple Notification Service Developer Guide*
+ [Creating a CloudWatch Events Rule That Triggers on an Event](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Rule.html) in the *Amazon CloudWatch Events User Guide*
+ [Configuring Amazon S3 Event Notifications](http://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html) in the *Amazon Simple Storage Service Developer Guide*

## Errors<a name="sqs-sse-troubleshooting-errors"></a>

When you work with Amazon SQS and AWS KMS, you might encounter errors\. The following list describes the errors and possible troubleshooting solutions\.

**KMSAccessDeniedException**  
The ciphertext references a key that doesn't exist or that you don't have access to\.  
HTTP Status Code: 400

**KMSDisabledException**  
The request was rejected because the specified CMK isn't enabled\.  
HTTP Status Code: 400

**KMSInvalidStateException**  
The request was rejected because the state of the specified resource isn't valid for this request\. For more information, see [How Key State Affects Use of a Customer Master Key](http://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the *AWS Key Management Service Developer Guide*\.  
HTTP Status Code: 400

**KMSNotFoundException**  
The request was rejected because the specified entity or resource can't be found\.  
HTTP Status Code: 400

**KMSOptInRequired**  
The AWS access key ID needs a subscription for the service\.  
HTTP Status Code: 403

**KMSThrottlingException**  
The request was denied due to request throttling\. For more information about throttling, see [Limits](http://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.  
HTTP Status Code: 400