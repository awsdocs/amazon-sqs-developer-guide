# Amazon SQS Standard queues<a name="standard-queues"></a>

Amazon SQS offers *standard* as the default queue type\. Standard queues support a nearly unlimited number of API calls per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\)\. Standard queues support at\-least\-once message delivery\. However, occasionally \(because of the highly distributed architecture that allows nearly unlimited throughput\), more than one copy of a message might be delivered out of order\. Standard queues provide best\-effort ordering which ensures that messages are generally delivered in the same order as they're sent\.

For information about how to create and configure standard queues using the Amazon SQS console, see [Creating an Amazon SQS queue \(console\)](sqs-configure-create-queue.md)\. For Java examples, see [Amazon SQS Java SDK examples](sqs-java-tutorials.md)\.

You can use standard message queues in many scenarios, as long as your application can process messages that arrive more than once and out of order, for example:
+ **Decouple live user requests from intensive background work** – Let users upload media while resizing or encoding it\.
+ **Allocate tasks to multiple worker nodes** – Process a high number of credit card validation requests\.
+ **Batch messages for future processing** – Schedule multiple entries to be added to a database\.

For quotas related to standard queues, see [Quotas related to queues](quotas-queues.md)\.

For best practices of working with standard queues, see [Recommendations for Amazon SQS standard and FIFO queues](sqs-standard-fifo-queue-best-practices.md)\.

## Message ordering<a name="standard-queues-message-order"></a>

A standard queue makes a best effort to preserve the order of messages, but more than one copy of a message might be delivered out of order\. If your system requires that order be preserved, we recommend using a [*FIFO \(First\-In\-First\-Out\) queue*](FIFO-queues.md) or adding sequencing information in each message so you can reorder the messages when they're received\.

## At\-least\-once delivery<a name="standard-queues-at-least-once-delivery"></a>

Amazon SQS stores copies of your messages on multiple servers for redundancy and high availability\. On rare occasions, one of the servers that stores a copy of a message might be unavailable when you receive or delete a message\.

If this occurs, the copy of the message isn't deleted on that unavailable server, and you might get that message copy again when you receive messages\. Design your applications to be *idempotent* \(they should not be affected adversely when processing the same message more than once\)\. 