# Using the Amazon SQS Message Group ID<a name="using-messagegroupid-property"></a>

The message group ID is the tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

## Interleaving Multiple Ordered Message Groups<a name="interleaving-multiple-ordered-message-groups"></a>

To interleave multiple ordered message groups within a single FIFO queue, use message group ID values \(for example, session data for multiple users\)\. In this scenario, multiple consumers can process the queue, but the session data of each user is processed in a FIFO manner\.

**Note**  
When messages that belong to a particular message group ID are invisible, no other consumer can process messages with the same message group ID\.

## Avoiding Processing Duplicates in a Multiple\-Producer/Consumer System<a name="avoding-processing-duplicates-in-multiple-producer-consumer-system"></a>

To avoid processing duplicate messages in a system with multiple producers and consumers where throughput and latency are more important than ordering, the producer should generate a unique message group ID for each message\.

**Note**  
In this scenario, duplicates are eliminated\. However, the ordering of message can't be guaranteed\.  
Any scenario with multiple producers and consumers increases the risk of inadvertently delivering a duplicate message if a worker doesn't process the message within the visibility timeout and the message becomes available to another worker\.

## Avoid Having a Large Backlog of Messages with the Same Message Group ID<a name="avoiding-having-a-large-backlog-of-messages-with-the-same-message-group-id"></a>

If your queue has a large backlog of 20,000 or more messages with the same message group ID, FIFO queues might be unable to return the messages that have a different message group ID but were sent to the queue at a later time until you successfully consume the messages from the backlog\.

**Note**  
A backlog of messages that have the same message group ID might build up because of a consumer that can't successfully process a message\. Message processing issues can occur because of an issue with the content of a message or because of a technical issue with the consumer\.  
To move away messages that can't be processed repeatedly, and to unblock the processing of other messages that have the same message group ID, consider setting up a [dead\-letter queue](sqs-dead-letter-queues.md) policy\.

## Avoid Reusing the Same Message Group ID with Virtual Queues<a name="avoiding-reusing-message-group-id-with-virtual-queues"></a>

To prevent messages with the the same message group ID sent to different [virtual queues](sqs-temporary-queues.md#virtual-queues) with the same host queue from blocking each other, avoid reusing the same message group ID with virtual queues\.