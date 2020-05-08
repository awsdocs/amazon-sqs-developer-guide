# Amazon SQS Standard queues<a name="standard-queues"></a>

Amazon SQS offers *standard* as the default queue type\. Standard queues support a nearly unlimited number of API calls per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\)\. Standard queues support at\-least\-once message delivery\. However, occasionally \(because of the highly distributed architecture that allows nearly unlimited throughput\), more than one copy of a message might be delivered out of order\. Standard queues provide best\-effort ordering which ensures that messages are generally delivered in the same order as they're sent\.

For information about creating standard queues with or without server\-side encryption using the AWS Management Console, the AWS SDK for Java \(and the `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` action\), or AWS CloudFormation, see [Tutorial: Creating an Amazon SQS queue](sqs-create-queue.md) and [Tutorial: Creating an Amazon SQS queue with Server\-Side Encryption \(SSE\)](sqs-create-queue-sse.md)\.

You can use standard message queues in many scenarios, as long as your application can process messages that arrive more than once and out of order, for example:
+ **Decouple live user requests from intensive background work** – Let users upload media while resizing or encoding it\.
+ **Allocate tasks to multiple worker nodes** – Process a high number of credit card validation requests\.
+ **Batch messages for future processing** – Schedule multiple entries to be added to a database\.

For quotas related to standard queues, see [Quotas related to queues](sqs-quotas.md#quotas-queues)\.

For best practices of working with standard queues, see [Recommendations for Amazon SQS Standard and FIFO \(First\-In\-First\-Out\) queues ](sqs-standard-fifo-queue-best-practices.md)\.

**Topics**
+ [Message ordering](#standard-queues-message-order)
+ [At\-least\-once delivery](#standard-queues-at-least-once-delivery)
+ [Working Java example for Standard queues](standard-queues-getting-started-java.md)

## Message ordering<a name="standard-queues-message-order"></a>

A standard queue makes a best effort to preserve the order of messages, but more than one copy of a message might be delivered out of order\. If your system requires that order be preserved, we recommend using a [*FIFO \(First\-In\-First\-Out\) queue*](FIFO-queues.md) or adding sequencing information in each message so you can reorder the messages when they're received\.

## At\-least\-once delivery<a name="standard-queues-at-least-once-delivery"></a>

Amazon SQS stores copies of your messages on multiple servers for redundancy and high availability\. On rare occasions, one of the servers that stores a copy of a message might be unavailable when you receive or delete a message\.

If this occurs, the copy of the message isn't deleted on that unavailable server, and you might get that message copy again when you receive messages\. Design your applications to be *idempotent* \(they should not be affected adversely when processing the same message more than once\)\. 