# Amazon SQS Standard Queues<a name="standard-queues"></a>

Amazon SQS offers *standard* as the default queue type\. Standard queues support a nearly unlimited number of transactions per second \(TPS\) per action\. Standard queues support at\-least\-once message delivery\. However, occasionally \(because of the highly distributed architecture that allows nearly unlimited throughput\), more than one copy of a message might be delivered out of order\. Standard queues provide best\-effort ordering which ensures that messages are generally delivered in the same order as they're sent\. For information about creating standard queues with or without server\-side encryption using the AWS Management Console, the AWS SDK for Java \(and the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` action\), or AWS CloudFormation, see [Tutorial: Creating an Amazon SQS Queue](sqs-create-queue.md) and [Tutorial: Creating an Amazon SQS Queue with Server\-Side Encryption \(SSE\)](sqs-create-queue-sse.md)\.

You can use standard message queues in many scenarios, as long as your application can process messages that arrive more than once and out of order, for example:
+ **Decouple live user requests from intensive background work** – Let users upload media while resizing or encoding it\.
+ **Allocate tasks to multiple worker nodes** – Process a high number of credit card validation requests\.
+ **Batch messages for future processing** – Schedule multiple entries to be added to a database\.

For best practices of working with standard queues, see [Recommendations for Amazon SQS Standard and FIFO \(First\-In\-First\-Out\) Queues ](sqs-standard-fifo-queue-best-practices.md)\.

**Topics**
+ [Message Ordering](#standard-queues-message-order)
+ [At\-Least\-Once Delivery](#standard-queues-at-least-once-delivery)
+ [Consuming Messages Using Short Polling](#consuming-messages-using-short-polling)
+ [Working Java Example for Standard Queues](standard-queues-getting-started-java.md)

## Message Ordering<a name="standard-queues-message-order"></a>

A standard queue makes a best effort to preserve the order of messages, but more than one copy of a message might be delivered out of order\. If your system requires that order be preserved, we recommend using a [*FIFO \(First\-In\-First\-Out\) queue*](FIFO-queues.md) or adding sequencing information in each message so you can reorder the messages when they're received\.

## At\-Least\-Once Delivery<a name="standard-queues-at-least-once-delivery"></a>

Amazon SQS stores copies of your messages on multiple servers for redundancy and high availability\. On rare occasions, one of the servers that stores a copy of a message might be unavailable when you receive or delete a message\.

If this occurs, the copy of the message isn't deleted on that unavailable server, and you might get that message copy again when you receive messages\. Design your applications to be *idempotent* \(they should not be affected adversely when processing the same message more than once\)\. 

## Consuming Messages Using Short Polling<a name="consuming-messages-using-short-polling"></a>

The process of consuming messages from a queue depends on whether you use short polling \(the default behavior\) or long polling\. For more information about long polling, see [Amazon SQS Long Polling](sqs-long-polling.md)\.

When you consume messages from a queue using short polling, Amazon SQS samples a subset of its servers \(based on a weighted random distribution\) and returns messages from only those servers\. Thus, a particular receive request might not return all of your messages\. However, if you have fewer than 1,000 messages in your queue, a subsequent request will return your messages\. If you keep consuming from your queues, Amazon SQS samples all of its servers, and you receive all of your messages\.

The following diagram shows the short\-polling behavior of messages returned from a standard queue after one of your system components makes a receive request\. Amazon SQS samples several of its servers \(in gray\) and returns messages A, C, D, and B from these servers\. Message E isn't returned for this request, but is returned for a subsequent request\.

![\[Message Sampling using Short (Standard) Polling\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview_Receive.png)