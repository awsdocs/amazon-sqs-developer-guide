# Additional recommendations for Amazon SQS FIFO queues<a name="sqs-additional-fifo-queue-recommendations"></a>

The following best practices can help you use the message deduplication ID and message group ID optimally\. For more information, see the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) and [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html) actions in the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Topics**
+ [Using the Amazon SQS message deduplication ID](using-messagededuplicationid-property.md)
+ [Using the Amazon SQS message group ID](using-messagegroupid-property.md)
+ [Using the Amazon SQS receive request attempt ID](using-receiverequestattemptid-request-parameter.md)