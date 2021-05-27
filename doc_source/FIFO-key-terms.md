# Key terms<a name="FIFO-key-terms"></a>

The following key terms can help you better understand the functionality of FIFO queues\. For more information, see the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Message deduplication ID**  
The token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.  
Message deduplication applies to an entire queue, not to individual message groups\.  
Amazon SQS continues to keep track of the message deduplication ID even after the message is received and deleted\.

**Message group ID**  
The tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

**Receive request attempt ID**  
The token used for deduplication of `ReceiveMessage` calls\.

**Sequence number**  
The large, non\-consecutive number that Amazon SQS assigns to each message\.