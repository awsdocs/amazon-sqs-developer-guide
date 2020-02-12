# Amazon SQS Visibility Timeout<a name="sqs-visibility-timeout"></a>

When a consumer receives and processes a message from a queue, the message remains in the queue\. Amazon SQS doesn't automatically delete the message\. Because Amazon SQS is a distributed system, there's no guarantee that the consumer actually receives the message \(for example, due to a connectivity issue, or due to an issue in the consumer application\)\. Thus, the consumer must delete the message from the queue after receiving and processing it\.

![\[Visibility Timeout\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-visibility-timeout-diagram.png)

Immediately after a message is received, it remains in the queue\. To prevent other consumers from processing the message again, Amazon SQS sets a *visibility timeout*, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message\. The default visibility timeout for a message is 30 seconds\. The minimum is 0 seconds\. The maximum is 12 hours\. For information about configuring visibility timeout for a queue using the AWS Management Console and for single or multiple messages using the AWS SDK for Java \(and the `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`, `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`, `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)`, `[ChangeMessageVisibility](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibility.html)`, and `[ChangeMessageVisibilityBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibilityBatch.html)` actions\), see [Tutorial: Configuring Visibility Timeout for an Amazon SQS Queue](sqs-configure-visibility-timeout-queue.md)\.

**Note**  
For standard queues, the visibility timeout isn't a guarantee against receiving a message twice\. For more information, see [At\-Least\-Once Delivery](standard-queues.md#standard-queues-at-least-once-delivery)\.  
FIFO queues allow the producer or consumer to attempt multiple retries:  
If the producer detects a failed `SendMessage` action, it can retry sending as many times as necessary, using the same message deduplication ID\. Assuming that the producer receives at least one acknowledgement before the deduplication interval expires, multiple retries neither affect the ordering of messages nor introduce duplicates\.
If the consumer detects a failed `ReceiveMessage` action, it can retry as many times as necessary, using the same receive request attempt ID\. Assuming that the consumer receives at least one acknowledgement before the visibility timeout expires, multiple retries don't affect the ordering of messages\.
When you receive a message with a message group ID, no more messages for the same message group ID are returned unless you delete the message or it becomes visible\.

**Topics**
+ [Inflight Messages](#inflight-messages)
+ [Setting the Visibility Timeout](#configuring-visibility-timeout)
+ [Changing the Visibility Timeout for a Message](#changing-message-visibility-timeout)
+ [Terminating the Visibility Timeout for a Message](#terminating-message-visibility-timeout)

## Inflight Messages<a name="inflight-messages"></a>

An Amazon SQS message has three basic states:

1. Sent to a queue by a producer\.

1. Received from the queue by a consumer\.

1. Deleted from the queue\.

A message is considered to be *stored* after it is sent to a queue by a producer, but not yet received from the queue by a consumer \(that is, between states 1 and 2\)\. There is no quota to the number of stored messages\. A message is considered to be *in flight* after it is received from a queue by a consumer, but not yet deleted from the queue \(that is, between states 2 and 3\)\. There is a quota to the number of inflight messages\.

**Important**  
Quotas that apply to inflight messages are unrelated to the *unlimited* number of stored messages\.

For most standard queues \(depending on queue traffic and message backlog\), there can be a maximum of approximately 120,000 inflight messages \(received from a queue by a consumer, but not yet deleted from the queue\)\. If you reach this quota while using [short polling](sqs-short-and-long-polling.md#sqs-short-polling), Amazon SQS returns the `OverLimit` error message\. If you use 