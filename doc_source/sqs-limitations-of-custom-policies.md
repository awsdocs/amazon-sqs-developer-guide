# Limitations of Custom Policies<a name="sqs-limitations-of-custom-policies"></a>

## Cross\-account access<a name="sqs-cross-account-access"></a>

Cross\-account permissions don't apply to the following actions:
+ `[AddPermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html)`
+ `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`
+ `[DeleteQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html)`
+ `[ListQueues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueues.html)`
+ `[ListQueueTags](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueueTags.html)`
+ `[RemovePermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_RemovePermission.html)`
+ `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`
+ `[TagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_TagQueue.html)`
+ `[UntagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_UntagQueue.html)`

## Condition keys<a name="sqs-condition-keys"></a>

Currently, Amazon SQS supports only a limited subset of the [condition keys available in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys)\. For more information, see [Amazon SQS API permissions: Actions and resource reference](sqs-api-permissions-reference.md)\.