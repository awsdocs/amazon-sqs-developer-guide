# Amazon SQS FIFO \(First\-In\-First\-Out\) Queues<a name="FIFO-queues"></a>

FIFO queues have all the capabilities of the [standard queue](standard-queues.md)\.

For information about creating FIFO queues with or without server\-side encryption using the AWS Management Console, the AWS SDK for Java \(and the `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` action\), or AWS CloudFormation, see [Tutorial: Creating an Amazon SQS Queue](sqs-create-queue.md) and [Tutorial: Creating an Amazon SQS Queue with Server\-Side Encryption \(SSE\)](sqs-create-queue-sse.md)\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0)

*FIFO \(First\-In\-First\-Out\)* queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated, for example:
+ Ensure that user\-entered commands are executed in the right order\.
+ Display the correct product price by sending price modifications in the right order\.
+ Prevent a student from enrolling in a course before registering for an account\.

FIFO queues also provide exactly\-once processing but have a limited number of transactions per second \(TPS\):
+ By default, FIFO queues support up to 3,000 messages per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\), with [batching](sqs-batch-api-actions.md)\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-sqs)\.
+ FIFO queues support up to 300 messages per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\) without batching\.

**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. The suffix counts towards the 80\-character queue name limit\. To determine whether a queue is [FIFO](#FIFO-queues), you can check whether the queue name ends with the suffix\.

For limits related to FIFO queues, see [Limits Related to Queues](sqs-limits.md#limits-queues)\.

For best practices of working with FIFO queues, see [Additional Recommendations for Amazon SQS FIFO Queues ](sqs-additional-fifo-queue-recommendations.md) and [Recommendations for Amazon SQS Standard and FIFO \(First\-In\-First\-Out\) Queues ](sqs-standard-fifo-queue-best-practices.md)\.

For information about compatibility of clients and services with FIFO queues, see [Compatibility](#FIFO-compatibility)\.

**Topics**
+ [Message Ordering](#FIFO-queues-message-order)
+ [Key Terms](#FIFO-key-terms)
+ [FIFO Delivery Logic](#FIFO-queues-understanding-logic)
+ [Exactly\-Once Processing](#FIFO-queues-exactly-once-processing)
+ [Moving from a Standard Queue to a FIFO Queue](#FIFO-queues-moving)
+ [Compatibility](#FIFO-compatibility)
+ [Working Java Example for FIFO Queues](FIFO-queues-getting-started-java.md)

## Message Ordering<a name="FIFO-queues-message-order"></a>

The FIFO queue improves upon and complements the [standard queue](standard-queues.md)\. The most important features of this queue type are [*FIFO \(First\-In\-First\-Out\) delivery*](#FIFO-queues-understanding-logic) and *[exactly\-once processing](#FIFO-queues-exactly-once-processing)*:
+ The order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it\.
+ Duplicates aren't introduced into the queue\.

In addition, FIFO queues support *message groups* that allow multiple ordered message groups within a single queue\.

## Key Terms<a name="FIFO-key-terms"></a>

The following key terms can help you better understand the functionality of FIFO queues\. For more information, see the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Message Deduplication ID**  
The token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.  
Message deduplication applies to an entire queue, not to individual message groups\.  
Amazon SQS continues to keep track of the message deduplication ID even after the message is received and deleted\.

**Message Group ID**  
The tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

**Receive Request Attempt ID**  
The token used for deduplication of `ReceiveMessage` calls\.

**Sequence Number**  
The large, non\-consecutive number that Amazon SQS assigns to each message\.

## FIFO Delivery Logic<a name="FIFO-queues-understanding-logic"></a>

The following concepts can help you better understand the sending of messages to and receiving messages from FIFO\.

**Sending Messages**  
If multiple messages are sent in succession to a FIFO queue, each with a distinct message deduplication ID, Amazon SQS stores the messages and acknowledges the transmission\. Then, each message can be received and processed in the exact order in which the messages were transmitted\.  
In FIFO queues, messages are ordered based on message group ID\. If multiple hosts \(or different threads on the same host\) send messages with the same message group ID to a FIFO queue, Amazon SQS stores the messages in the order in which they arrive for processing\. To ensure that Amazon SQS preserves the order in which messages are sent and received, ensure that each producer uses a unique message group ID to send all its messages\.  
FIFO queue logic applies only per message group ID\. Each message group ID represents a distinct ordered message group within an Amazon SQS queue\. For each message group ID, all messages are sent and received in strict order\. However, messages with different message group ID values might be sent and received out of order\. You must associate a message group ID with a message\. If you don't provide a message group ID, the action fails\. If you require a single group of ordered messages, provide the same message group ID for messages sent to the FIFO queue\.

**Receiving Messages**  
You can't request to receive messages with a specific message group ID\.  
When receiving messages from a FIFO queue with multiple message group IDs, Amazon SQS first attempts to return as many messages with the same message group ID as possible\. This allows other consumers to process messages with a different message group ID\.  
It is possible to receive up to 10 messages in a single call using the `MaxNumberOfMessages` request parameter of the `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` action\. These messages retain their FIFO order and can have the same message group ID\. Thus, if there are fewer than 10 messages available with the same message group ID, you might receive messages from another message group ID, in the same batch of 10 messages, but still in FIFO order\.

**Retrying Multiple Times**  
FIFO queues allow the producer or consumer to attempt multiple retries:  
+ If the producer detects a failed `SendMessage` action, it can retry sending as many times as necessary, using the same message deduplication ID\. Assuming that the producer receives at least one acknowledgement before the deduplication interval expires, multiple retries neither affect the ordering of messages nor introduce duplicates\.
+ If the consumer detects a failed `ReceiveMessage` action, it can retry as many times as necessary, using the same receive request attempt ID\. Assuming that the consumer receives at least one acknowledgement before the visibility timeout expires, multiple retries don't affect the ordering of messages\.
+ When you receive a message with a message group ID, no more messages for the same message group ID are returned unless you delete the message or it becomes visible\.

## Exactly\-Once Processing<a name="FIFO-queues-exactly-once-processing"></a>

Unlike standard queues, FIFO queues don't introduce duplicate messages\. FIFO queues help you avoid sending duplicates to a queue\. If you retry the `SendMessage` action within the 5\-minute deduplication interval, Amazon SQS doesn't introduce any duplicates into the queue\.

To configure deduplication, you must do one of the following:
+ Enable content\-based deduplication\. This instructs Amazon SQS to use a SHA\-256 hash to generate the message deduplication ID using the body of the message—but not the attributes of the message\. For more information, see the documentation on the `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`, `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`, and `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` actions in the *Amazon Simple Queue Service API Reference*\.
+ Explicitly provide the message deduplication ID \(or view the sequence number\) for the message\. For more information, see the documentation on the `[SendMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)`, `[SendMessageBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)`, and `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` actions in the *Amazon Simple Queue Service API Reference*\.

## Moving from a Standard Queue to a FIFO Queue<a name="FIFO-queues-moving"></a>

If you have an existing application that uses standard queues and you want to take advantage of the ordering or exactly\-once processing features of FIFO queues, you need to configure the queue and your application correctly\.

**Note**  
You can't convert an existing standard queue into a FIFO queue\. To make the move, you must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue\.

Use the following checklist to ensure that your application works correctly with a FIFO queue\.
+ By default, FIFO queues support up to 3,000 messages per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\), with [batching](sqs-batch-api-actions.md)\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-sqs)\. FIFO queues support up to 300 messages per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\) without batching\.
+ FIFO queues don't support per\-message delays, only per\-queue delays\. If your application sets the same value of the `DelaySeconds` parameter on each message, you must modify your application to remove the per\-message delay and set `DelaySeconds` on the entire queue instead\.
+ Every message sent to a FIFO queue requires a message group ID\. If you don't need multiple ordered message groups, specify the same message group ID for all your messages\.
+ Before sending messages to a FIFO queue, confirm the following:
  + If your application can send messages with identical message bodies, you can modify your application to provide a unique message deduplication ID for each sent message\.
  + If your application sends messages with unique message bodies, you can enable content\-based deduplication\.
+ You don't have to make any code changes to your consumer\. However, if it takes a long time to process messages and your visibility timeout is set to a high value, consider adding a receive request attempt ID to each `ReceiveMessage` action\. This allows you to retry receive attempts in case of networking failures and prevents queues from pausing due to failed receive attempts\.

For more information, see the * [Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

## Compatibility<a name="FIFO-compatibility"></a>

**Clients**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

**Services**  
If your application uses multiple AWS services, or a mix of AWS and external services, it is important to understand which service functionality doesn't support FIFO queues\.  
Some AWS or external services that send notifications to Amazon SQS might not be compatible with FIFO queues, despite allowing you to set a FIFO queue as a target\.  
The following features of AWS services aren't currently compatible with FIFO queues:  
+ [Auto Scaling Lifecycle Hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)
+ [AWS IoT Rule Actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html)
+ [AWS Lambda Dead\-Letter Queues](https://docs.aws.amazon.com/lambda/latest/dg/dlq.html)
For information about compatibility of other services with FIFO queues, see your service documentation\.