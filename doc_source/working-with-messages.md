# Working with Amazon SQS messages<a name="working-with-messages"></a>

The following guidelines can help you process messages efficiently using Amazon SQS\.

**Topics**
+ [Processing messages in a timely manner](#processing-messages-timely-manner)
+ [Handling request errors](#handling-request-errors)
+ [Setting up long polling](#setting-up-long-polling)
+ [Capturing problematic messages](#capturing-problematic-messages)
+ [Setting up dead\-letter queue retention](#setting-up-dead-letter-queue-retention)
+ [Avoiding inconsistent message processing](#avoiding-inconsistent-message-processing)
+ [Implementing request\-response systems](#implementing-request-response-systems)

## Processing messages in a timely manner<a name="processing-messages-timely-manner"></a>

Setting the visibility timeout depends on how long it takes your application to process and delete a message\. For example, if your application requires 10 seconds to process a message and you set the visibility timeout to 15 minutes, you must wait for a relatively long time to attempt to process the message again if the previous processing attempt fails\. Alternatively, if your application requires 10 seconds to process a message but you set the visibility timeout to only 2 seconds, a duplicate message is received by another consumer while the original consumer is still working on the message\.

To ensure that there is sufficient time to process messages, use one of the following strategies:
+ If you know \(or can reasonably estimate\) how long it takes to process a message, extend the message's *visibility timeout* to the maximum time it takes to process and delete the message\. For more information, see [Configuring the Visibility Timeout](sqs-visibility-timeout.md#configuring-visibility-timeout)\.
+ If you don't know how long it takes to process a message, create a *heartbeat* for your consumer process: Specify the initial visibility timeout \(for example, 2 minutes\) and then—as long as your consumer still works on the message—keep extending the visibility timeout by 2 minutes every minute\. 
**Important**  
The maximum visibility timeout is 12 hours from the time that Amazon SQS receives the message\. Extending the visibility timeout does not reset the 12\-hour maximum\. If your consumer needs longer than 12 hours, consider using Step Functions\. 

## Handling request errors<a name="handling-request-errors"></a>

To handle request errors, use one of the following strategies:
+ If you use an AWS SDK, you already have automatic *retry and backoff* logic at your disposal\. For more information, see [Error Retries and Exponential Backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html) in the *Amazon Web Services General Reference*\.
+ If you don't use the AWS SDK features for retry and backoff, allow a pause \(for example, 200 ms\) before retrying the [ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) action after receiving no messages, a timeout, or an error message from Amazon SQS\. For subsequent use of `ReceiveMessage` that gives the same results, allow a longer pause \(for example, 400 ms\)\. 

## Setting up long polling<a name="setting-up-long-polling"></a>

When the wait time for the `ReceiveMessage` API action is greater than 0, *long polling* is in effect\. The maximum long polling wait time is 20 seconds\. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses \(when there are no messages available for a `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` request\) and false empty responses \(when messages are available but aren't included in a response\)\. For more information, see [Amazon SQS short and long polling](sqs-short-and-long-polling.md)\.

To ensure optimal message processing, use the following strategies:
+ In most cases, you can set the `ReceiveMessage` wait time to 20 seconds\. If 20 seconds is too long for your application, set a shorter `ReceiveMessage` wait time \(1 second minimum\)\. If you don't use an AWS SDK to access Amazon SQS, or if you configure an AWS SDK to have a shorter wait time, you might have to modify your Amazon SQS client to either allow longer requests or use a shorter wait time for long polling\.
+ If you implement long polling for multiple queues, use one thread for each queue instead of a single thread for all queues\. Using a single thread for each queue allows your application to process the messages in each of the queues as they become available, while using a single thread for polling multiple queues might cause your application to become unable to process messages available in other queues while the application waits \(up to 20 seconds\) for the queue which doesn't have any available messages\.

**Important**  
To avoid HTTP errors, ensure that the HTTP response timeout for `ReceiveMessage` requests is longer than the `WaitTimeSeconds` parameter\. For more information, see [ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)\.

## Capturing problematic messages<a name="capturing-problematic-messages"></a>

To capture all messages that can't be processed, and to ensure the correctness of CloudWatch metrics, configure a [dead\-letter queue](sqs-dead-letter-queues.md)\.
+ The redrive policy redirects messages to a dead\-letter queue after the source queue fails to process a message a specified number of times\.
+ Using a dead\-letter queue decreases the number of messages and reduces the possibility of exposing you to *poison pill* messages \(messages that are received but can't be processed\)\.
+ Including a poison pill message in a queue can distort the [`ApproximateAgeOfOldestMessage`](sqs-available-cloudwatch-metrics.md) CloudWatch metric by giving an incorrect age of the poison pill message\. Configuring a dead\-letter queue helps avoid false alarms when using this metric\.

## Setting up dead\-letter queue retention<a name="setting-up-dead-letter-queue-retention"></a>

The expiration of a message is always based on its original enqueue timestamp\. When a message is moved to a dead\-letter queue, the enqueue timestamp remains unchanged\. For example, if a message spends 1 day in the original queue before being moved to a dead\-letter queue, and the retention period of the dead\-letter queue is set to 4 days, the message is deleted from the dead\-letter queue after 3 days\. Thus, it is a best practice to always set the retention period of a dead\-letter queue to be longer than the retention period of the original queue\.

## Avoiding inconsistent message processing<a name="avoiding-inconsistent-message-processing"></a>

Because Amazon SQS is a distributed system, it is possible for a consumer to not receive a message even when Amazon SQS marks the message as delivered while returning successfully from a `ReceiveMessage` API method call\. In this case, Amazon SQS records the message as delivered at least once, although the consumer has never received it\. Because no additional attempts to deliver messages are made under these conditions, we don't recommend setting the number of maximum receives to 1 for a [dead\-letter queue](sqs-dead-letter-queues.md)\.

## Implementing request\-response systems<a name="implementing-request-response-systems"></a>

When implementing a request\-response or remote procedure call \(RPC\) system, keep the following best practices in mind:
+ Don't create reply queues *per message*\. Instead, create reply queues on startup, *per producer*, and use a correlation ID message attribute to map replies to requests\.
+ Don't let your producers share reply queues\. This can cause a producer to receive response messages intended for another producer\.

For more information about implementing the request\-response pattern using the Temporary Queue Client, see [Request\-response messaging pattern \(virtual queues\)](sqs-temporary-queues.md#request-reply-messaging-pattern)\.