# Additional Identifiers for Amazon SQS FIFO Queues<a name="sqs-additional-fifo-queue-identifiers"></a>

For more information about the following identifiers, see [Exactly\-Once Processing](FIFO-queues.md#FIFO-queues-exactly-once-processing) and the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

## Message Deduplication ID<a name="sqs-fifo-queue-messagededuplicationid"></a>

The token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.

## Message Group ID<a name="sqs-fifo-queue-messagegroupid"></a>

The tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

## Sequence Number<a name="sqs-fifo-queue-sequence-number"></a>

The large, non\-consecutive number that Amazon SQS assigns to each message\.