# Partitions and data distribution for high throughput for SQS FIFO queues<a name="partitions-and-data-distribution"></a>

Amazon SQS stores FIFO queue data in partitions\. A *partition* is an allocation of storage for a queue that is automatically replicated across multiple Availability Zones within an AWS Region\. You don't manage partitions\. Instead, Amazon SQS handles partition management\.

For FIFO queues, Amazon SQS modifies the number of partitions in a queue in the following situations:
+ If the current request rate approaches or exceeds what the existing partitions can support, additional partitions are allocated until the queue reaches the regional quota\. For information on quotas, see [Quotas related to messages](quotas-messages.md)\.
+ If the current partitions have low utilization, the number of partitions may be reduced\.

Partition management occurs automatically in the background and is transparent to your applications\. Your queue and messages are available at all times\.

## Distributing data by message group IDs<a name="data-distribution-message-group-id"></a>

To add a message to a FIFO queue, Amazon SQS uses the value of each message’s message group ID as input to an internal hash function\. The output value from the hash function determines which partition stores the message\.

The following diagram shows a queue that spans multiple partitions\. The queue’s message group ID is based on item number\. Amazon SQS uses its hash function to determine where to store a new item; in this case, it's based on the hash value of the string `item0`\. Note that the items are stored in the same order in which they are added to the queue\. Each item's location is determined by the hash value of its message group ID\.

![\[A queue spans multiple partitions.\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/fifo-documentation-single.png)

**Note**  
Amazon SQS is optimized for uniform distribution of items across a FIFO queue's partitions, regardless of the number of partitions\. AWS recommends that you use message group IDs that can have a large number of distinct values\. 

## Optimizing partition utilization<a name="data-distribution-partition-limitations"></a>

Each partition supports up to 3,000 messages per second with batching, or up to 300 messages per second for send, receive, and delete operations\.

When using batch APIs, each message is routed based on the process described in [Distributing data by message group IDs ](#data-distribution-message-group-id)\. Messages that are routed to the same partition are grouped and processed in a single transaction\.

To optimize partition utilization for the `SendMessageBatch` API, AWS recommends batching messages with the same message group IDs when possible\.

To optimize partition utilization for the `DeleteMessageBatch` and `ChangeMessageVisibilityBatch` APIs, AWS recommends using `ReceiveMessage` requests with the `MaxNumberOfMessages` parameter set to 10, and batching the receipt\-handles returned by a single `ReceiveMessage` request\.

In the following example, a batch of messages with various message group IDs is sent\. The batch is split into three groups, each of which counts against the quota for the partition\.

![\[A batch of messages with different message group IDs is stored in partitions.\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/fifo-documentation-batch.png)

**Note**  
Amazon SQS only guarantees that messages with the same message group ID are grouped within a batch request\. Depending on the output of the internal hash function and the number of partitions, messages with different message group IDs might be grouped\. Since the hash function or number of partitions can change at any time, messages that are grouped at one point may not be grouped later\.