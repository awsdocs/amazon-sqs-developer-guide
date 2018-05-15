# Amazon SQS Long Polling<a name="sqs-long-polling"></a>

*Long polling* helps reduce the cost of using Amazon SQS by eliminating the number of empty responses \(when there are no messages available for a `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` request\) and false empty responses \(when messages are available but aren't included in a response\)\. For information about enabling long polling for a new or existing queue using the AWS Management Console or the AWS SDK for Java \(and the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`, `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`, and `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` actions\), see the [Tutorial: Configuring Long Polling for an Amazon SQS Queue](sqs-configure-long-polling-for-queue.md) tutorial\. For best practices, see [Setting Up Long Polling](working-with-messages.md#setting-up-long-polling)\.

Long polling offers the following benefits:
+ Eliminate empty responses by allowing Amazon SQS to wait until a message is available in a queue before sending a response\. Unless the connection times out, the response to the `ReceiveMessage` request contains at least one of the available messages, up to the maximum number of messages specified in the `ReceiveMessage` action\.
+ Eliminate false empty responses by querying all—rather than a subset of—Amazon SQS servers\.
+ Return messages as soon as they become available\.

## Differences Between Long and Short Polling<a name="sqs-short-long-polling-differences"></a>

By default, Amazon SQS uses *short polling*, querying only a subset of its servers \(based on a weighted random distribution\), to determine whether any messages are available for a response\.

Short polling occurs when the `WaitTimeSeconds` parameter of a `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` request is set to `0` in one of two ways:
+ The `ReceiveMessage` call sets `WaitTimeSeconds` to `0`\.
+ The `ReceiveMessage` call doesn’t set `WaitTimeSeconds`, but the queue attribute [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) is set to `0`\.

**Note**  
For the `WaitTimeSeconds` parameter of the `ReceiveMessage` action, a value set between `1` and `20` has priority over any value set for the queue attribute `ReceiveMessageWaitTimeSeconds`\.