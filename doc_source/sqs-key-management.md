# Key management<a name="sqs-key-management"></a>

Amazon SQS integrates with the AWS Key Management Service \(KMS\) to manage [KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) for server\-side encryption \(SSE\)\. See [Encryption at rest](sqs-server-side-encryption.md) for SSE information and key management definitions\. Amazon SQS uses KMS keys to validate and secure the data keys that encrypt and decrypt the messages\. The following sections provide information about working with KMS keys and data keys in the Amazon SQS service\.

**Topics**
+ [Configuring AWS KMS permissions](#sqs-what-permissions-for-sse)
+ [Understanding the data key reuse period](#sqs-how-does-the-data-key-reuse-period-work)
+ [Estimating AWS KMS costs](#sqs-estimate-kms-usage-costs)
+ [AWS KMS errors](#sqs-sse-troubleshooting-errors)

## Configuring AWS KMS permissions<a name="sqs-what-permissions-for-sse"></a>

Every KMS key must have a key policy\. Note that you cannot modify the key policy of an AWS managed KMS key for Amazon SQS\. The policy for this KMS key includes permissions for all principals in the account \(that are authorized to use Amazon SQS\) to use encrypted queues\. 

For a customer managed KMS key, you must configure the key policy to add permissions for each queue producer and consumer\. To do this, you name the producer and consumer as users in the KMS key policy\. For more information about AWS KMS permissions, see [AWS KMS resources and operations](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#kms-resources-operations) or [AWS KMS API permissions reference](https://docs.aws.amazon.com/kms/latest/developerguide/kms-api-permissions-reference.html) in the *AWS Key Management Service Developer Guide*\.

Alternatively, you can specify the required permissions in an IAM policy assigned to the principals that produce and consume encrypted messages\. For more information, see [Using IAM Policies with AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html) in the *AWS Key Management Service Developer Guide*\.

**Note**  
While you can configure global permissions to send to and receive from Amazon SQS, AWS KMS requires explicitly naming the full ARN of KMS keys in specific regions in the `Resource` section of an IAM policy\.

### Configure KMS permissions for AWS services<a name="compatibility-with-aws-services"></a>

Several AWS services act as event sources that can send events to Amazon SQS queues\. To allow these event sources to work with encrypted queues, you must create a customer managed KMS key and add permissions in the key policy for the service to use the required AWS KMS API methods\. Perform the following steps to configure the permissions\.

1. Create a customer managed KMS key\. For more information, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

1. To allow the AWS service event source to use the `kms:GenerateDataKey` and `kms:Decrypt` API methods, add the following statement to the KMS key key policy\.

   ```
   {
      "Version": "2012-10-17",
         "Statement": [{
            "Effect": "Allow",
            "Principal": {
               "Service": "service.amazonaws.com"
            },
            "Action": [
               "kms:GenerateDataKey",
               "kms:Decrypt"
            ],
            "Resource": "*"
          }]
   }
   ```

   Replace "service" in the above example with the *Service name* of the event source\. Event sources include the following services\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-key-management.html)

1.  [Configure an existing SSE queue](sqs-configure-sse-existing-queue.md) using the ARN of your KMS key\.

1. Provide the ARN of the encrypted queue to the event source\.

### Configure KMS permissions for producers<a name="send-to-encrypted-queue"></a>

When the [data key reuse period](#sqs-how-does-the-data-key-reuse-period-work) expires, the producer's next call to `SendMessage` or `SendMessageBatch` also triggers calls to `kms:GenerateDataKey` and `kms:Decrypt`\. The call to `kms:Decrypt` is to verify the integrity of the new data key before using it\. Therefore, the producer must have the `kms:GenerateDataKey` and `kms:Decrypt` permissions for the KMS key\. 

Add the following statement to the IAM policy of the producer\. Remember to use the correct ARN values for the key resource and the queue resource\.

```
{
   "Version": "2012-10-17",
      "Statement": [{
         "Effect": "Allow",
         "Action": [
            "kms:GenerateDataKey",
            "kms:Decrypt"
         ],
         "Resource":  "arn:aws:kms:us-east-2:123456789012:key/1234abcd-12ab-34cd-56ef-1234567890ab"
         }, {
         "Effect": "Allow",
         "Action": [
            "sqs:SendMessage" 
         ],
         "Resource": "arn:aws:sqs:*:123456789012:MyQueue"
      }]
}
```

### Configure KMS permissions for consumers<a name="receive-from-encrypted-queue"></a>

When the data key reuse period expires, the consumer's next call to `ReceiveMessage` also triggers a call to `kms:Decrypt`, to verify the integrity of the new data key before using it\. Therefore, the consumer must have the `kms:Decrypt` permission for any KMS key that is used to encrypt the messages in the specified queue\. If the queue acts as a [dead\-letter queue](sqs-dead-letter-queues.md), the consumer must also have the `kms:Decrypt` permission for any KMS key that is used to encrypt the messages in the source queue\. Add the following statement to the IAM policy of the consumer\. Remember to use the correct ARN values for the key resource and the queue resource\.

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

### Configure KMS permissions with confused deputy protection<a name="sqs-adding-confused-deputy-protection"></a>

When the principal in a key policy statement is an [AWS service principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html#principal-services), you can use the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) or [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition keys to protect against the [confused deputy scenario](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. To use these condition keys, set the value to the Amazon Resource Name \(ARN\) of the resource that is being encrypted\. If you don't know the ARN of the resource, use `aws:SourceAccount` instead\. 

In this KMS key policy, a specific resource from *service* that is owned by account `111122223333` is allowed to call KMS for `Decrypt` and `GenerateDataKey` actions, which occur during SSE usage of Amazon SQS\.

```
{
	"Version": "2012-10-17",
	"Statement": [{
		"Effect": "Allow",
		"Principal": {
            "Service": "<replaceable>service</replaceable>.amazonaws.com"
		},
		"Action": [
			"kms:GenerateDataKey",
			"kms:Decrypt"
		],
		"Resource": "*",
		"Condition": {
			"ArnEquals": {
				"aws:SourceArn": [
					"arn:aws:service::111122223333:resource"
				]
			}
		}
	}]
}
```

When using SSE enabled Amazon SQS queues, the following services support `aws:SourceArn`:
+ Amazon SNS
+ Amazon S3
+ CloudWatch Events
+ AWS Lambda
+ CodeBuild
+ Amazon Connect Customer Profiles
+ AWS Auto Scaling
+ Amazon Chime

## Understanding the data key reuse period<a name="sqs-how-does-the-data-key-reuse-period-work"></a>

The [ data key reuse period](sqs-server-side-encryption.md#sqs-sse-key-terms) defines the maximum duration for Amazon SQS to reuse the same data key\. When the data key reuse period ends, Amazon SQS generates a new data key\. Note the following guidelines about the reuse period\.
+ A shorter reuse period provides better security but results in more calls to AWS KMS, which might incur charges beyond the Free Tier\.
+ Although the data key is cached separately for encryption and for decryption, the reuse period applies to both copies of the data key\.
+ When the data key reuse period ends, the next call to `SendMessage` or `SendMessageBatch` typically triggers a call to the AWS KMS `GenerateDataKey` method to get a new data key\. Also, the next calls to `SendMessage` and `ReceiveMessage` will each trigger a call to AWS KMS `Decrypt` to verify the integrity of the data key before using it\.
+ [Principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) \(AWS accounts or IAM users\) don't share data keys \(messages sent by unique principals always get unique data keys\)\. Thus, the volume of calls to AWS KMS is a multiple of the number of unique principals in use during the data key reuse period:

## Estimating AWS KMS costs<a name="sqs-estimate-kms-usage-costs"></a>

To predict costs and better understand your AWS bill, you might want to know how often Amazon SQS uses your KMS key\.

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
In general, producing principals incur double the cost of consuming principals\. For more information, see [Understanding the data key reuse period](#sqs-how-does-the-data-key-reuse-period-work)\.  
If the producer and consumer have different IAM users, the cost increases\.

The following are example calculations\. For exact pricing information, see [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing/)\.

### Example 1: Calculating the number of AWS KMS API calls for 2 principals and 1 queue<a name="example-1-queue-2-principals"></a>

This example assumes the following:
+ The billing period is January 1\-31 \(2,678,400 seconds\)\.
+ The data key reuse period is set to 5 minutes \(300 seconds\)\.
+ There is 1 queue\.
+ There is 1 producing principal and 1 consuming principal\.

```
2,678,400 / 300 * (2 * 1 + 1) = 26,784
```

### Example 2: Calculating the number of AWS KMS API calls for multiple producers and consumers and 2 queues<a name="example-2-queues-multiple-principals"></a>

This example assumes the following:
+ The billing period is February 1\-28 \(2,419,200 seconds\)\.
+ The data key reuse period is set to 24 hours \(86,400 seconds\)\.
+ There are 2 queues\.
+ The first queue has 3 producing principals and 1 consuming principal\.
+ The second queue has 5 producing principals and 2 consuming principals\.

```
(2,419,200 / 86,400 * (2 * 3 + 1)) + (2,419,200 / 86,400 * (2 * 5 + 2)) = 532
```

## AWS KMS errors<a name="sqs-sse-troubleshooting-errors"></a>

When you work with Amazon SQS and AWS KMS, you might encounter errors\. The following references describe the errors and possible troubleshooting solutions\.
+ [ Common AWS KMS errors](https://docs.aws.amazon.com/kms/latest/APIReference/CommonErrors.html)
+ [AWS KMS Decrypt errors](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html#API_Decrypt_Errors)
+ [AWS KMS GenerateDataKey errors](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html#API_GenerateDataKey_Errors)