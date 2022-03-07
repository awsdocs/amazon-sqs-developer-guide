# Configuring server\-side encryption \(SSE\) for a queue \(console\)<a name="sqs-configure-sse-existing-queue"></a>

To protect the data in a queue's messages, you can enable server\-side encryption \(SSE\) for a queue\. Amazon SQS integrates with the Amazon Web Services Key Management Service \(Amazon Web Services KMS\) to manage [KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) for server\-side encryption \(SSE\)\. For information about using SSE, see [Encryption at rest](sqs-server-side-encryption.md)\.



The KMS key that you assign to your queue must have a key policy that includes permissions for all principals that are authorized to use the queue\. For information, see [Key Management](sqs-key-management.md)\.

If you aren't the owner of the KMS key, or if you log in with an account that doesn't have `kms:ListAliases` and `kms:DescribeKey` permissions, you won't be able to view information about the KMS key on the Amazon SQS console\. Ask the owner of the KMS key to grant you these permissions\. For more information, see [Key Management](sqs-key-management.md)\.

When you [create](sqs-configure-create-queue.md) or [edit](sqs-configure-edit-queue.md) a queue, you can configure SSE\-KMS\.

**To configure SSE\-KMS for an existing queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. Choose a queue, and then choose **Edit**\.

1. Expand **Encryption**\.

1. For **Server\-side encryption**, choose **Enabled**\.

1. Select **AWS Key Management Service key \(SSE\-KMS\)**\.

   The console displays the **Description**, the **Account**, and the **KMS key ARN** of the KMS key\.

1. Specify the KMS key ID for the queue\. For more information, see [Key terms](sqs-server-side-encryption.md#sqs-sse-key-terms)\.

   1. Choose the **Choose a KMS key alias** option\.

   1. The default key is the Amazon Web Services managed KMS key for Amazon SQS\. To use this key, choose it from the **KMS key** list\. 

   1. To use a custom KMS key from your Amazon Web Services account, choose it from the **KMS key** list\. For instructions on creating custom KMS keys, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *Amazon Web Services Key Management Service Developer Guide*\.

   1. To use a custom KMS key that is not in the list, or a custom KMS key from another Amazon Web Services account, choose **Enter the KMS key alias** and enter the KMS key Amazon Resource Name \(ARN\)\.

1. \(Optional\) For **Data key reuse period**, specify a value between 1 minute and 24 hours\. The default is 5 minutes\. For more information, see [Understanding the data key reuse period](sqs-key-management.md#sqs-how-does-the-data-key-reuse-period-work)\.

1. When you finish configuring SSE\-KMS, choose **Save**\.