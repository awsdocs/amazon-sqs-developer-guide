# Key Management<a name="sqs-key-management"></a>

The following sections provide information about working with keys managed in AWS Key Management Service \(AWS KMS\)\.

**Topics**
+ [Understanding the Data Key Reuse Period](#sqs-how-does-the-data-key-reuse-period-work)
+ [Estimating AWS KMS Costs](#sqs-estimate-kms-usage-costs)
+ [Configuring AWS KMS Permissions](#sqs-what-permissions-for-sse)
+ [AWS KMS Errors](#sqs-sse-troubleshooting-errors)

## Understanding the Data Key Reuse Period<a name="sqs-how-does-the-data-key-reuse-period-work"></a>

Amazon SQS uses a single customer master key \(either the AWS managed CMK for Amazon SQS or a custom CMK\) to provide [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/workflow.html#envelope_encryption) and decryption of multiple Amazon SQS messages during the *data key reuse period*\. To make the most of the [data key reuse period](sqs-server-side-encryption.md#sqs-sse-key-terms), keep the following in mind:
+ A shorter reuse period provides better security but results in more calls to AWS KMS, which might incur charges beyond the Free Tier\.
+ Although the data key is cached separately for encryption and for decryption, the reuse period applies to both copies of the data key\.
+ [Principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) \(AWS accounts or IAM users\) don't share data keys \(messages sent by unique principals always get unique data keys\)\. Thus, the volume of calls to AWS KMS is a multiple of the number of unique principals in use during the data key reuse period:
  + When you send messages using the `SendMessage` or `SendMessageBatch` action, Amazon SQS typically calls the AWS KMS `GenerateDataKey` and `Decrypt` actions once per every data key reuse period\.
**Note**  
For each data key that AWS KMS generates, SSE calls the `Decrypt` action to verify the integrity of the data key before using it\.
  + When you receive messages using the `ReceiveMessage` action, Amazon SQS typically calls the AWS KMS `Decrypt` action once per every data key reuse period\.

## Estimating AWS KMS Costs<a name="sqs-estimate-kms-usage-costs"></a>

To predict costs and better understand your AWS bill, you might want to know how often Amazon SQS uses your customer master key \(CMK\)\.

**Note**  
Although the following formula can give you a very good idea of expected costs, actual costs might be higher because of the distributed nature of Amazon SQS\.

To calculate the number of API requests \(`R`\) *per queue*, use the following formula:

```
R = B / D * (2 * P + C)
```

`B` is the billing period \(in seconds\)\.

`D` is the [data key reuse period](sqs-server-side-encryption.md#sqs-sse-key-terms) \(in seconds\)\.

`P` is the number of producing [principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) that send to the Amazon SQS queue\.

`C` is the number of consuming principals that receive from the Amazon SQS queue\.

**Important**  
In general, producing principals incur double the cost of consuming principals\. For more information, see [Understanding the Data Key Reuse Period](#sqs-how-does-the-data-key-reuse-period-work)  
If the producer and consumer have different IAM users, the cost increases\.

The following are example calculations\. For exact pricing information, see [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing/)\.

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

## Configuring AWS KMS Permissions<a name="sqs-what-permissions-for-sse"></a>

Before you can use SSE, you must configure AWS KMS key policies to allow encryption of queues and encryption and decryption of messages\. For examples and more information about AWS KMS permissions, see [AWS KMS API Permissions: Actions and Resources Reference](https://docs.aws.amazon.com/kms/latest/developerguide/kms-api-permissions-reference.html) in the *AWS Key Management Service Developer Guide*\.

**Note**  
You can also manage permissions for KMS keys using IAM policies\. For more information, see [Using IAM Policies with AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html)\.  
While you can configure global permissions to send to and receive from Amazon SQS, AWS KMS requires explicitly naming the full ARN of CMKs in specific regions in the `Resource` section of an IAM policy\.  
For each data key that AWS KMS generates, SSE calls the `Decrypt` action to verify the integrity of the data key before using it\.

You must also ensure that the key policies of the customer master key \(CMK\) allow the necessary permissions\. To do this, name the principals that produce and consume encrypted messages in Amazon SQS as users in the CMK key policy\. 

Alternatively, you can specify the required AWS KMS actions and CMK ARN in an IAM policy assigned to the principals that produce and consume encrypted messages in Amazon SQS\. For more information, see [Managing Access to AWS KMS CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#managing-access) in the *AWS Key Management Service Developer Guide*\.

### Allow a User to Send Single or Batched Messages to a Queue with SSE<a name="send-to-encrypted-queue"></a>

The producer must have the `kms:GenerateDataKey` and `kms:Decrypt` permissions for the customer master key \(CMK\)\. For each data key that AWS KMS generates, SSE calls the `Decrypt` action to verify the integrity of the data key before using it\.

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

### Allow a User to Receive Messages from a Queue with SSE<a name="receive-from-encrypted-queue"></a>

The consumer must have the `kms:Decrypt` permission for any customer master key \(CMK\) that is used to encrypt the messages in the specified queue\. For each data key that AWS KMS generates, SSE calls the `Decrypt` action to verify the integrity of the data key before using it\. If the queue acts as a [dead\-letter queue](sqs-dead-letter-queues.md), the consumer must also have the `kms:Decrypt` permission for any CMK that is used to encrypt the messages in the source queue\.

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

### Enable Compatibility between Event Sources from AWS Services and Encrypted Queues<a name="compatibility-with-aws-services"></a>

Several AWS services send events to Amazon SQS queues\. To allow these event sources to work with encrypted queues, you must perform the following steps\.

1. Use a customer managed CMK\. For more information, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

1. To allow the AWS service to have the `kms:GenerateDataKey*` and `kms:Decrypt` permissions, add the following statement to the CMK policy\.

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
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-key-management.html)

1. [Create a new SSE queue](sqs-create-queue-sse.md) or [configure an existing SSE queue](sqs-configure-sse-existing-queue.md) using the ARN of your CMK\.

1. Provide the ARN of the encrypted queue to the event source\.

## AWS KMS Errors<a name="sqs-sse-troubleshooting-errors"></a>

When you work with Amazon SQS and AWS KMS, you might encounter errors\. The following list describes the errors and possible troubleshooting solutions\.

**KMSAccessDeniedException**  
The ciphertext references a key that doesn't exist or that you don't have access to\.  
HTTP Status Code: 400

**KMSDisabledException**  
The request was rejected because the specified CMK isn't enabled\.  
HTTP Status Code: 400

**KMSInvalidStateException**  
The request was rejected because the state of the specified resource isn't valid for this request\. For more information, see [How Key State Affects Use of a Customer Master Key](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the *AWS Key Management Service Developer Guide*\.  
HTTP Status Code: 400

**KMSNotFoundException**  
The request was rejected because the specified entity or resource can't be found\.  
HTTP Status Code: 400

**KMSOptInRequired**  
The AWS access key ID needs a subscription for the service\.  
HTTP Status Code: 403

**KMSThrottlingException**  
The request was denied due to request throttling\. For more information about throttling, see [Limits](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.  
HTTP Status Code: 400