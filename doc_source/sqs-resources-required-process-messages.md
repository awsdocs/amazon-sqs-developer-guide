# Resources Required to Process Messages<a name="sqs-resources-required-process-messages"></a>

To help you estimate the resources you need to process queued messages, Amazon SQS can determine the approximate number of delayed, visible, and not visible messages in a queue\. For more information about visibility, see [Visibility Timeout](sqs-visibility-timeout.md)\.

**Note**  
For standard queues, the result is approximate because of the distributed architecture of Amazon SQS\. In most cases, the count should be close to the actual number of messages in the queue\.  
For FIFO queues, the result is exact\.

The following table lists the API action to use\.


| To do this\.\.\. | Use this action | Use this `AttributeName` | 
| --- | --- | --- | 
|  Get the approximate number of messages in the queue\.  |  `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`  |  `ApproximateNumberOfMessages`  | 
|  Get the approximate number of messages that are pending to be added to the queue\.  |  `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`   |  `ApproximateNumberOfMessagesDelayed`  | 
|  Get the approximate number of messages in the queue that are not visible \(messages in flight\)\.  |  `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`   |  `ApproximateNumberOfMessagesNotVisible`  | 