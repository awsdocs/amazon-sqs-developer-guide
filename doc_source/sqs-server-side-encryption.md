# Encryption at rest<a name="sqs-server-side-encryption"></a>

Server\-side encryption \(SSE\) lets you transmit sensitive data in encrypted queues\. SSE protects the contents of messages in queues using keys managed in AWS Key Management Service \(AWS KMS\)\. For information about managing SSE using the AWS Management Console, see [Configuring server\-side encryption \(SSE\) for a queue \(console\)](sqs-configure-sse-existing-queue.md)\. 

 For information about managing SSE using the AWS SDK for Java \(and the `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`, `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`, and `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)` actions\), see the following examples:
+ [Using server\-side encryption \(SSE\)](sqs-java-configure-sse.md)
+ [Configure KMS permissions for AWS services](sqs-key-management.md#compatibility-with-aws-services)

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/Mw1NVpJsOZc?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/Mw1NVpJsOZc?rel=0&amp;controls=0&amp;showinfo=0)

SSE encrypts messages as soon as Amazon SQS receives them\. The messages are stored in encrypted form and Amazon SQS decrypts messages only when they are sent to an authorized consumer\.

**Important**  
All requests to queues with SSE enabled must use HTTPS and [Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\.  
An [encrypted queue](#sqs-server-side-encryption) that uses the default key \(AWS managed CMK for Amazon SQS\) cannot invoke a Lambda function in a different AWS account\.  
Some features of AWS services that can send notifications to Amazon SQS using the AWS Security Token Service `[AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)` action are compatible with SSE but work *only with standard queues:*  
[Auto Scaling Lifecycle Hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)
[AWS Lambda Dead\-Letter Queues](https://docs.aws.amazon.com/lambda/latest/dg/dlq.html)
For information about compatibility of other services with encrypted queues, see [Configure KMS permissions for AWS services](sqs-key-management.md#compatibility-with-aws-services) and your service documentation\.

AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. When you use Amazon SQS with AWS KMS, the [data keys](#sqs-sse-key-terms) that encrypt your message data are also encrypted and stored with the data they protect\.

The following are benefits of using AWS KMS:
+ You can create and manage [customer master keys \(CMKs\)](#sqs-sse-key-terms) yourself\.
+ You can also use the AWS managed CMK for Amazon SQS, which is unique for each account and region\.
+ The AWS KMS security standards can help you meet encryption\-related compliance requirements\.

For more information, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide* and the [AWS Key Management Service Cryptographic Details](https://d0.awsstatic.com/whitepapers/KMS-Cryptographic-Details.pdf) whitepaper\.

**Topics**
+ [Encryption scope](#sqs-encryption-what-does-sse-encrypt)
+ [Key terms](#sqs-sse-key-terms)

## Encryption scope<a name="sqs-encryption-what-does-sse-encrypt"></a>

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

## Key terms<a name="sqs-sse-key-terms"></a>

The following key terms can help you better understand the functionality of SSE\. For detailed descriptions, see the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Data key**  
The data encryption key \(DEK\) responsible for encrypting the contents of Amazon SQS messages\.  
For more information, see [Data Keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) in the *AWS Key Management Service Developer Guide* in the *AWS Encryption SDK Developer Guide*\.

**Data key reuse period**  
The length of time, in seconds, for which Amazon SQS can reuse a data key to encrypt or decrypt messages before calling AWS KMS again\. An integer representing seconds, between 60 seconds \(1 minute\) and 86,400 seconds \(24 hours\)\. The default is 300 \(5 minutes\)\. For more information, see [Understanding the data key reuse period](sqs-key-management.md#sqs-how-does-the-data-key-reuse-period-work)\.  
In the unlikely event of being unable to reach AWS KMS, Amazon SQS continues to use the cached data key until a connection is reestablished\.

**Customer master key ID**  
The alias, alias ARN, key ID, or key ARN of an AWS managed customer master key \(CMK\) or a custom CMK—in your account or in another account\. While the alias of the AWS managed CMK for Amazon SQS is always `alias/aws/sqs`, the alias of a custom CMK can, for example, be `alias/MyAlias`\. You can use these CMKs to protect the messages in Amazon SQS queues\.   
Keep the following in mind:  
+ If you don't specify a custom CMK, Amazon SQS uses the AWS managed CMK for Amazon SQS\.
+ The first time you use the AWS Management Console to specify the AWS managed CMK for Amazon SQS for a queue, AWS KMS creates the AWS managed CMK for Amazon SQS\.
+ Alternatively, the first time you use the `SendMessage` or `SendMessageBatch` action on a queue with SSE enabled, AWS KMS creates the AWS managed CMK for Amazon SQS\.
You can create CMKs, define the policies that control how CMKs can be used, and audit CMK usage using the **Customer managed keys** section of the AWS KMS console or the `[CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html)` AWS KMS action\. For more information, see [Customer Master Keys \(CMKs\)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) and [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more examples of CMK identifiers, see [KeyId](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html#API_DescribeKey_RequestParameters) in the *AWS Key Management Service API Reference*\. For information about finding CMK identifiers, see [Find the Key ID and ARN](https://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html#find-cmk-id-arn) in the *AWS Key Management Service Developer Guide*\.  
There are additional charges for using AWS KMS\. For more information, see [Estimating AWS KMS costs](sqs-key-management.md#sqs-estimate-kms-usage-costs) and [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing)\.

**Envelope Encryption**  
The security of your encrypted data depends in part on protecting the data key that can decrypt it\. Amazon SQS uses the CMK to encrypt the data key and then the encrypted data key is stored with the encrypted message\. This practice of using a master key to encrypt data keys is known as envelope encryption\.   
For more information, see [Envelope Encryption](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/how-it-works.html#envelope-encryption) in the *AWS Encryption SDK Developer Guide*\.