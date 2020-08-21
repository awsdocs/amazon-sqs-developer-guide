# Using the Amazon SQS message deduplication ID<a name="using-messagededuplicationid-property"></a>

The message deduplication ID is the token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.

**Note**  
Message deduplication applies to an entire queue, not to individual message groups\.  
Amazon SQS continues to keep track of the message deduplication ID even after the message is received and deleted\.

## Providing the message deduplication ID<a name="providing-message-deduplication-id"></a>

The producer should provide message deduplication ID values for each message send in the following scenarios:
+ Messages sent with identical message bodies that Amazon SQS must treat as unique\.
+ Messages sent with identical content but different message attributes that Amazon SQS must treat as unique\.
+ Messages sent with different content \(for example, retry counts included in the message body\) that Amazon SQS must treat as duplicates\.

## Enabling deduplication for a single\-Producer/Consumer system<a name="single-producer-single-consumer"></a>

If you have a single producer and a single consumer and the messages are unique because an application\-specific message ID is included in the body of the message, follow these best practices:
+ Enable content\-based deduplication for the queue \(each of your messages has a unique body\)\. The producer can omit the message deduplication ID\.
+ Although the consumer isn't required to provide a receive request attempt ID for each request, it's a best practice because it allows fail\-retry sequences to execute faster\.
+ You can retry send or receive requests because they don't interfere with the ordering of messages in FIFO queues\.

## Designing for outage recovery scenarios<a name="designing-for-outage-recovery-scenarios"></a>

The deduplication process in FIFO queues is time\-sensitive\. When designing your application, ensure that both the producer and the consumer can recover in case of a client or network outage\.
+ The producer must be aware of the deduplication interval of the queue\. Amazon SQS has a *minimum* deduplication interval of 5 minutes\. Retrying `SendMessage` requests after the deduplication interval expires can introduce duplicate messages into the queue\. For example, a mobile device in a car sends messages whose order is important\. If the car loses cellular connectivity for a period of time before receiving an acknowledgement, retrying the request after regaining cellular connectivity can create a duplicate\.
+ The consumer must have a visibility timeout that minimizes the risk of being unable to process messages before the visibility timeout expires\. You can extend the visibility timeout while the messages are being processed by calling the `ChangeMessageVisibility` action\. However, if the visibility timeout expires, another consumer can immediately begin to process the messages, causing a message to be processed multiple times\. To avoid this scenario, configure a [dead\-letter queue](sqs-dead-letter-queues.md)\.

## Working with visibility timeouts<a name="working-with-visibility-timeouts"></a>

For optimal performance, set the [visibility timeout](sqs-visibility-timeout.md) to be larger than the AWS SDK read timeout\. This applies to using the `ReceiveMessage` API action with either [short polling](sqs-short-and-long-polling.md#sqs-short-polling) or [long polling](sqs-short-and-long-polling.md)\.