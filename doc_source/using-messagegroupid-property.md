# Using the Amazon SQS Message Group ID<a name="using-messagegroupid-property"></a>

The message group ID is the tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.


+ [Interleaving Multiple Ordered Message Groups](#interleaving-multiple-ordered-message-groups)
+ [Avoiding Processing Duplicates in a Multiple\-Producer/Consumer System](#avoding-processing-duplicates-in-multiple-producer-consumer-system)

## Interleaving Multiple Ordered Message Groups<a name="interleaving-multiple-ordered-message-groups"></a>

To interleave multiple ordered message groups within a single FIFO queue, use message group ID values \(for example, session data for multiple users\)\. In this scenario, multiple consumers can process the queue, but the session data of each user is processed in a FIFO manner\.

**Note**  
When messages that belong to a particular message group ID are invisible, no other consumer can process messages with the same message group ID\.

## Avoiding Processing Duplicates in a Multiple\-Producer/Consumer System<a name="avoding-processing-duplicates-in-multiple-producer-consumer-system"></a>

To avoid processing duplicate messages in a system with multiple producers and consumers where throughput and latency are more important than ordering, the producer should generate a unique message group ID for each message\.

**Note**  
In this scenario, duplicates are eliminated\. However, the ordering of message can't be guaranteed\.  
Any scenario with multiple producers and consumers increases the risk of inadvertently delivering a duplicate message if a worker doesn't process the message within the visibility timeout and the message becomes available to another worker\.