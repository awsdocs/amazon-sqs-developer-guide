# Resources required to process Amazon SQS messages<a name="sqs-resources-required-process-messages"></a>

To help you estimate the resources you need to process queued messages, Amazon SQS can determine the approximate number of delayed, visible, and not visible messages in a queue\. For more information about visibility, see [Amazon SQS visibility timeout](sqs-visibility-timeout.md)\.

**Note**  
For standard queues, the result is approximate because of the distributed architecture of Amazon SQS\. In most cases, the count should be close to the actual number of messages in the queue\.  
For FIFO queues, the result is exact\.

The following table lists the attribute name to use with the `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)` action:


| Task | Attribute name | 
| --- | --- | 
| Get the approximate number of messages available for retrieval from the queue\. | ApproximateNumberOfMessages | 
| Get the approximate number of messages in the queue that are delayed and not available for reading immediately\. This can happen when the queue is configured as a delay queue or when a message has been sent with a delay parameter\.  | ApproximateNumberOfMessagesDelayed | 
| Get the approximate number of messages that are in flight\. Messages are considered to be in flight if they have been sent to a client but have not yet been deleted or have not yet reached the end of their visibility window\. | ApproximateNumberOfMessagesNotVisible | 