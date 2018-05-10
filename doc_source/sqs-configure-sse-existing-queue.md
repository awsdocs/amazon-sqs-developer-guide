# Tutorial: Configuring Server\-Side Encryption \(SSE\) for an Existing Amazon SQS Queue<a name="sqs-configure-sse-existing-queue"></a>

Server\-side encryption \(SSE\) for Amazon SQS is available in all regions where Amazon SQS is available, except for the China Regions\. You can enable SSE for a queue to protect its data\. For more information about using SSE, see [Protecting Data Using Server\-Side Encryption \(SSE\) and AWS KMS ](sqs-server-side-encryption.md)\.

**Important**  
All requests to queues with SSE enabled must use HTTPS and [Signature Version 4](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\.  
When you disable SSE, messages remain encrypted\. You must receive and decrypt a message to view its contents\.

The following example shows how to enable, disable, and configure SSE for an existing Amazon SQS queue\.

**Topics**
+ [Configure SSE for an Amazon SQS Queue Using the AWS Management Console](#sqs-configure-sse-existing-queue-console)
+ [Configure SSE for an Amazon SQS Queue Using the AWS SDK for Java](#sqs-configure-sse-existing-queue-java)

## AWS Management Console<a name="sqs-configure-sse-existing-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Configure Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-queue.png)

   The **Configure *QueueName*** dialog box is displayed\.

1. To enable or disable SSE, use the **Use SSE** check box\.

1. Specify the customer master key \(CMK\) ID\. For more information, see [Key Terms](sqs-server-side-encryption.md#sqs-sse-key-terms)\. 

   For each CMK type, the **Description**, **Account**, and **Key ARN** of the CMK are displayed\.
**Important**  
If you aren't the owner of the CMK, or if you log in with an account that doesn't have the `kms:ListAliases` and `kms:DescribeKey` permissions, you won't be able to view information about the CMK on the Amazon SQS console\.  
Ask the owner of the CMK to grant you these permissions\. For more information, see the [AWS KMS API Permissions: Actions and Resources Reference](http://docs.aws.amazon.com/kms/latest/developerguide/kms-api-permissions-reference.html) in the *AWS Key Management Service Developer Guide*\.
   + To use the AWS\-managed CMK for Amazon SQS, select it from the list\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-server-side-encryption-default-service-cmk.png)
**Note**  
Keep the following in mind:  
If you don't specify a custom CMK, Amazon SQS uses the AWS\-managed CMK for Amazon SQS\. For instructions on creating custom CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
The first time you use the AWS Management Console to specify the AWS\-managed CMK for Amazon SQS for a queue, AWS KMS creates the AWS\-managed CMK for Amazon SQS\.
Alternatively, the first time you use the `SendMessage` or `SendMessageBatch` action on a queue with SSE enabled, AWS KMS creates the AWS\-managed CMK for Amazon SQS\.
   + To use a custom CMK from your AWS account, select it from the list\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-server-side-encryption-custom-cmk.png)
**Note**  
For instructions on creating custom CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
   + To use a custom CMK ARN from your AWS account or from another AWS account, select **Enter an existing CMK ARN** from the list and type or copy the CMK\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-server-side-encryption-custom-cmk-arn.png)

1. \(Optional\) For **Data key reuse period**, specify a value between 1 minute and 24 hours\. The default is 5 minutes\. For more information, see [How Does the Data Key Reuse Period Work?](sqs-server-side-encryption.md#sqs-how-does-the-data-key-reuse-period-work)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-server-side-encryption-data-key-reuse-period.png)

1. Choose **Save Changes**\.

   Your changes are applied to the queue\.

## AWS SDK for Java<a name="sqs-configure-sse-existing-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

Before you can use SSE, you must configure AWS KMS key policies to allow encryption of queues and encryption and decryption of messages\. You must also ensure that the key policies of the customer master key \(CMK\) allow the necessary permissions\. For more information, see [What AWS KMS Permissions Do I Need to Use SSE for Amazon SQS?](sqs-server-side-encryption.md#sqs-what-permissions-for-sse)\.

1. Obtain the customer master key \(CMK\) ID\. For more information, see [Key Terms](sqs-server-side-encryption.md#sqs-sse-key-terms)\.
**Note**  
Keep the following in mind:  
If you don't specify a custom CMK, Amazon SQS uses the AWS\-managed CMK for Amazon SQS\. For instructions on creating custom CMKs, see [Creating Keys](http://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
The first time you use the AWS Management Console to specify the AWS\-managed CMK for Amazon SQS for a queue, AWS KMS creates the AWS\-managed CMK for Amazon SQS\.
Alternatively, the first time you use the `SendMessage` or `SendMessageBatch` action on a queue with SSE enabled, AWS KMS creates the AWS\-managed CMK for Amazon SQS\.

1. To enable server\-side encryption, specify the CMK ID by setting the `KmsMasterKeyId` attribute of the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` or `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action\.

   The following code example enables SSE for an existing queue using the AWS\-managed CMK for Amazon SQS:

   ```
   final SetQueueAttributesRequest setAttributesRequest = new SetQueueAttributesRequest();
   setAttributesRequest.setQueueUrl(queueUrl);
          
   // Enable server-side encryption by specifying the alias ARN of the
   // AWS-managed CMK for Amazon SQS.
   final String kmsMasterKeyAlias = "arn:aws:kms:us-east-2:123456789012:alias/aws/sqs";
   attributes.put("KmsMasterKeyId", kmsMasterKeyAlias);
          
   final SetQueueAttributesResult setAttributesResult = client.setQueueAttributes(setAttributesRequest);
   ```

   To disable server\-side encryption for an existing queue, set the `KmsMasterKeyId` attribute to an empty string using the `SetQueueAttributes` action\.
**Important**  
`null` isn't a valid value for `KmsMasterKeyId`\.

1. \(Optional\) Specify the length of time, in seconds, for which Amazon SQS can [reuse a data key](sqs-server-side-encryption.md#sqs-sse-key-terms) to encrypt or decrypt messages before calling AWS KMS\. Set the `KmsDataKeyReusePeriodSeconds` attribute of the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` or `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action\. Possible values may be between 60 seconds \(1 minute\) and 86,400 seconds \(24 hours\)\. If you don't specify a value, the default value of 300 seconds \(5 minutes\) is used\.

   The following code example sets the data key reuse period to 60 seconds \(1 minute\):

   ```
   // (Optional) Specify the length of time, in seconds, for which Amazon SQS can reuse 
   // a data key to encrypt or decrypt messages before calling AWS KMS again.
   attributes.put("KmsDataKeyReusePeriodSeconds", "60");
   ```

For information about retrieving the attributes of a queue, see [Examples](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html#API_GetQueueAttributes_Examples) in the *Amazon Simple Queue Service API Reference*\.

To retrieve the CMK ID or the data key reuse period for a particular queue, use the `KmsMasterKeyId` and `KmsDataKeyReusePeriodSeconds` attributes of the `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)` action\.

For information about switching a queue to a different CMK with the same alias, see [Updating an Alias](http://docs.aws.amazon.com/kms/latest/developerguide/programming-aliases.html#update-alias) in the *AWS Key Management Service Developer Guide*\.