# Configuring server\-side encryption \(SSE\) for a queue \(console\)<a name="sqs-configure-sse-existing-queue"></a>

To protect the data in a queue's messages, you can enable server\-side encryption \(SSE\) for a queue\. Amazon SQS integrates with the AWS Key Management Service \(AWS KMS\) to manage [customer master keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) \(CMKs\) for server\-side encryption \(SSE\)\. For information about using SSE, see [Encryption at rest](sqs-server-side-encryption.md)\.

The CMK that you assign to your queue must have a key policy that includes permissions for all principals that are authorized to use the queue\. For information, see [Key Management](sqs-key-management.md)\.

If you aren't the owner of the CMK, or if you log in with an account that doesn't have `kms:ListAliases` and `kms:DescribeKey` permissions, you won't be able to view information about the CMK on the Amazon SQS console\. Ask the owner of the CMK to grant you these permissions\. For more information, see [Key Management](sqs-key-management.md)\.

When you [create](sqs-configure-create-queue.md) or [edit](sqs-configure-edit-queue.md) a queue, you can configure SSE\.

**To configure SSE for an existing queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. Choose a queue and choose **Edit**\. 

1. Scroll to the **Encryption** section\.

1. In the **Encryption** section, choose **Enabled** to enable SSE\.

   The console displays the **Description**, the **Account**, and the **CMK ARN** of the CMK\.

1. Specify the CMK ID for the queue\. For more information, see [Key terms](sqs-server-side-encryption.md#sqs-sse-key-terms)\.

   1. Choose the **Choose a CMK alias** option\.

   1. The default key is the AWS managed CMK for Amazon SQS\. To use this key, choose it from the **Customer master key** list\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-server-side-encryption-default-service-cmk.png)

   1. To use a custom CMK from your AWS account, choose it from the **Customer master key** list\. For instructions on creating custom CMKs, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

   1. To use a custom CMK that is not in the list, or a custom CMK from another AWS account, choose **Enter the CMK alias** and enter the CMK Amazon Resource Name \(ARN\)\.

1. \(Optional\) For **Data key reuse period**, specify a value between 1 minute and 24 hours\. The default is 5 minutes\. For more information, see [Understanding the data key reuse period](sqs-key-management.md#sqs-how-does-the-data-key-reuse-period-work)\.

1. When you finish configuring SSE, choose **Save**\.