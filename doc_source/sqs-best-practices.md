# Best practices for Amazon SQS<a name="sqs-best-practices"></a>

These best practices can help you make the most of Amazon SQS\.

## Recommendations for Amazon SQS Standard and FIFO \(First\-In\-First\-Out\) queues<a name="sqs-standard-fifo-queue-best-practices"></a>

The following best practices can help you reduce costs and process messages efficiently using Amazon SQS\.

**Topics**
+ [Working with Amazon SQS messages](working-with-messages.md)
+ [Reducing Amazon SQS costs](reducing-costs.md)
+ [Moving from an Amazon SQS Standard queue to a FIFO queue](moving-from-high-throughout-queue-to-FIFO-queue.md)

## Additional recommendations for Amazon SQS FIFO queues<a name="sqs-additional-fifo-queue-recommendations"></a>

The following best practices can help you use the message deduplication ID and message group ID optimally\. For more information, see the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) and [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html) actions in the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Topics**
+ [Using the Amazon SQS message deduplication ID](using-messagededuplicationid-property.md)
+ [Using the Amazon SQS message group ID](using-messagegroupid-property.md)
+ [Using the Amazon SQS receive request attempt ID](using-receiverequestattemptid-request-parameter.md)