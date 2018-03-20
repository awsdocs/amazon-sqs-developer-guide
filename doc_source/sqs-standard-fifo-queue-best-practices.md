# Recommendations for Standard and FIFO \(First\-In\-First\-Out\) Queues<a name="sqs-standard-fifo-queue-best-practices"></a>

The following guidelines can help you reduce costs and process messages efficiently using Amazon SQS\.

## Working with Messages<a name="working-with-messages"></a>

### Processing Messages in a Timely Manner<a name="processing-messages-timely-manner"></a>

Setting the visibility timeout depends on how long it takes your application to process and delete a message\. For example, if your application requires 10 seconds but you set the visibility timeout to 15 minutes, you might have to wait too long to process a message\. Alternatively, if your application requires 10 seconds but you set the visibility timeout to 2 seconds, a duplicate might be received by another receiver\. 

To ensure that there is sufficient time to process a message, use one of the following strategies:

+ If you know \(or can reasonably estimate\) how long it takes to process a message, extend the message's *visibility timeout* to the maximum time it takes to process and delete the message\. For more information, see [Configuring the Visibility Timeout](sqs-visibility-timeout.md#configuring-visibility-timeout) and [Changing a Message's Visibility Timeout](sqs-visibility-timeout.md#changing-message-visibility-timeout)\.

+ If you don't know how long it takes to process a message, specify the initial visibility timeout \(for example, 2 minutes\) and the period of time after which you can check whether the message is processed \(for example, 1 minute\)\. If the message isn't processed, extend the visibility timeout \(for example, to 3 minutes\)\.

**Note**  
If you need to extend the visibility timeout for longer than 12 hours, consider using [AWS Step Functions](https://aws.amazon.com/step-functions/)\. 

### Handling Request Errors<a name="handling-request-errors"></a>

To handle request errors, use one of the following strategies:

+ If you use an AWS SDK, you already have automatic *retry and backoff* logic at your disposal\. For more information, see [Error Retries and Exponential Backoff in AWS](http://docs.aws.amazon.com/general/latest/gr/api-retries.html) in the *Amazon Web Services General Reference*\.

+ If you don't use the AWS SDK features for retry and backoff, allow a pause \(for example, 200 ms\) before retrying the [ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) action after receiving no messages, a timeout, or an error message from Amazon SQS\. For subsequent use of `ReceiveMessage` that gives the same results, allow a longer pause \(for example, 400 ms\)\. 

### Capturing Problematic Messages<a name="capturing-problematic-messages"></a>

To capture all messages that can't be processed, and to ensure the correctness of CloudWatch metrics, configure a [dead\-letter queue](sqs-dead-letter-queues.md)\.

+ The redrive policy redirects messages to a dead\-letter queue after the source queue fails to process a message a specified number of times\.

+ Using a dead\-letter queue decreases the number of messages and reduces the possibility of exposing you to *poison pill* messages \(messages that are received but can't be processed\)\.

+ Including a poison pill message in a queue can distort the [`ApproximateAgeOfOldestMessage`](sqs-available-cloudwatch-metrics.md) CloudWatch metric by giving an incorrect age of the poison pill message\. Configuring a dead\-letter queue helps avoid false alarms when using this metric\.

### Setting Up Dead\-Letter Queue Retention<a name="setting-up-dead-letter-queue-retention"></a>

The expiration of a message is always based on its original enqueue timestamp\. When a message is moved to a [dead\-letter queue](sqs-dead-letter-queues.md), the enqueue timestamp remains unchanged\. For example, if a message spends 1 day in the original queue before being moved to a dead\-letter queue, and the retention period of the dead\-letter queue is set to 4 days, the message is deleted from the dead\-letter queue after 3 days\. Thus, it is a best practice to always set the retention period of a dead\-letter queue to be longer than the retention period of the original queue\.

### Avoiding Inconsistent Message Processing<a name="avoiding-inconsistent-message-processing"></a>

To avoid inconsistent message processing by standard queues, avoid setting the number of maximum receives to 1 when you configure a dead\-letter queue\.

**Important**  
In some unlikely scenarios, if you set the number of maximum receives to 1, any time a `ReceiveMessage` call fails, a message might be moved to a dead\-letter queue without being received\.

## Reducing Costs<a name="reducing-costs"></a>

### Batching Message Actions<a name="batching-message-actions"></a>

To reduce costs, batch your message actions:

+ To send, receive, and delete messages, and to change the message visibility timeout for multiple messages with a single action, use the [Amazon SQS batch API actions](sqs-batch-api-actions.md)\.

+ To combine client\-side buffering with request batching, use long polling together with the [ buffered asynchronous client](sqs-batch-api-actions.md#sqs-client-side-buffering-request-batching) included with the AWS SDK for Java\.
**Note**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

### Using the Appropriate Polling Mode<a name="using-appropriate-polling-mode"></a>

To take advantage of additional potential reduced cost or near\-instantaneous response, use one of the following polling modes:

+ Long polling lets you consume messages from your Amazon SQS queue as soon as they become available\. 

  + To reduce the cost of using Amazon SQS and to decrease the number of empty receives to an empty queue \(responses to the `ReceiveMessage` action which return no messages\), enable long polling\. For more information, see [Amazon SQS Long Polling](sqs-long-polling.md)\.

  + To increase efficiency when polling for multiple threads with multiple receives, decrease the number of threads\.

  + Long polling is preferable over short polling in most cases\.

+ Short polling returns responses immediately, even if the polled Amazon SQS queue is empty\. 

  + To satisfy the requirements of an application that expects immediate responses to the `ReceiveMessage` request, use short polling\.

  + Short polling is billed the same as long polling\.

## Moving from a Standard Queue to a FIFO Queue<a name="moving-from-high-throughout-queue-to-FIFO-queue"></a>

+ If you're not setting the `DelaySeconds` parameter on each message, you can move to a FIFO queue by providing a message group ID for every sent message\. For more information, see [Moving from a Standard Queue to a FIFO Queue](FIFO-queues.md#FIFO-queues-moving)\.