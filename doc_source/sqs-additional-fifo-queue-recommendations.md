# Additional Recommendations for FIFO Queues<a name="sqs-additional-fifo-queue-recommendations"></a>

The following guidelines can help you use the message deduplication ID and message group ID optimally\. For more information, see the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) and [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html) actions in the *[Amazon Simple Queue Service API Reference](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

## Using the Message Deduplication ID<a name="using-messagededuplicationid-property"></a>

The message deduplication ID is the token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.

**Note**  
Message deduplication applies to an entire queue, not to individual message groups\.  
Amazon SQS continues to keep track of the message deduplication ID even after the message is received and deleted\.

### Providing the Message Deduplication ID<a name="providing-message-deduplication-id"></a>

The producer should provide message deduplication ID values for each message send in the following scenarios:

+ Messages sent with identical message bodies that Amazon SQS must treat as unique\.

+ Messages sent with identical content but different message attributes that Amazon SQS must treat as unique\.

+ Messages sent with different content \(for example, retry counts included in the message body\) that Amazon SQS must treat as duplicates\.

### Enabling Deduplication for a Single\-Producer/Consumer System<a name="single-producer-single-consumer"></a>

If you have a single producer and a single consumer and the messages are unique because an application\-specific message ID is included in the body of the message, follow these guidelines:

+ Enable content\-based deduplication for the queue \(each of your messages has a unique body\)\. The producer can omit the message deduplication ID\.

+ Although the consumer isn't required to provide a receive request attempt ID for each request, it's a best practice because it allows fail\-retry sequences to execute faster\.

+ You can retry send or receive requests because they don't interfere with the ordering of messages in FIFO queues\.

### Designing for Outage Recovery Scenarios<a name="designing-for-outage-recovery-scenarios"></a>

The deduplication process in FIFO queues is time\-sensitive\. When designing your application, ensure that both the producer and the consumer can recover in case of a client or network outage\.

+ The producer must be aware of the deduplication interval of the queue\. Amazon SQS has a *minimum* deduplication interval of 5 minutes\. Retrying `SendMessage` requests after the deduplication interval expires can introduce duplicate messages into the queue\. For example, a mobile device in a car sends messages whose order is important\. If the car loses cellular connectivity for a period of time before receiving an acknowledgement, retrying the request after regaining cellular connectivity can create a duplicate\.

+ The consumer must have a visibility timeout that minimizes the risk of being unable to process messages before the visibility timeout expires\. You can extend the visibility timeout while the messages are being processed by calling the `ChangeMessageVisibility` action\. However, if the visibility timeout expires, another consumer can immediately begin to process the messages, causing a message to be processed multiple times\. To avoid this scenario, configure a [dead\-letter queue](sqs-dead-letter-queues.md)\.

## Using the Message Group ID<a name="using-messagegroupid-property"></a>

The message group ID is the tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

### Interleaving Multiple Ordered Message Groups<a name="interleaving-multiple-ordered-message-groups"></a>

To interleave multiple ordered message groups within a single FIFO queue, use message group ID values \(for example, session data for multiple users\)\. In this scenario, multiple consumers can process the queue, but the session data of each user is processed in a FIFO manner\.

**Note**  
When messages that belong to a particular message group ID are invisible, no other consumer can process messages with the same message group ID\.

### Avoiding Processing Duplicates in a Multiple\-Producer/Consumer System<a name="avoding-processing-duplicates-in-multiple-producer-consumer-system"></a>

To avoid processing duplicate messages in a system with multiple producers and consumers where throughput and latency are more important than ordering, the producer should generate a unique message group ID for each message\.

**Note**  
In this scenario, duplicates are eliminated\. However, the ordering of message can't be guaranteed\.  
Any scenario with multiple producers and consumers increases the risk of inadvertently delivering a duplicate message if a worker doesn't process the message within the visibility timeout and the message becomes available to another worker\.

## Using the Receive Request Attempt ID<a name="using-receiverequestattemptid-request-parameter"></a>

The receive request attempt ID is the large, non\-consecutive number that Amazon SQS assigns to each message\.

During a long\-lasting network outage that causes connectivity issues between your SDK and Amazon SQS, it's a best practice to provide the receive request attempt ID and to retry with the same receive request attempt ID if the SDK operation fails\.