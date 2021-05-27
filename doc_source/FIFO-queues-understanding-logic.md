# FIFO delivery logic<a name="FIFO-queues-understanding-logic"></a>

The following concepts can help you better understand the sending of messages to and receiving messages from FIFO\.

**Sending messages**  
If multiple messages are sent in succession to a FIFO queue, each with a distinct message deduplication ID, Amazon SQS stores the messages and acknowledges the transmission\. Then, each message can be received and processed in the exact order in which the messages were transmitted\.  
In FIFO queues, messages are ordered based on message group ID\. If multiple hosts \(or different threads on the same host\) send messages with the same message group ID to a FIFO queue, Amazon SQS stores the messages in the order in which they arrive for processing\. To make sure that Amazon SQS preserves the order in which messages are sent and received, each producer should use a unique message group ID to send all its messages\.  
FIFO queue logic applies only per message group ID\. Each message group ID represents a distinct ordered message group within an Amazon SQS queue\. For each message group ID, all messages are sent and received in strict order\. However, messages with different message group ID values might be sent and received out of order\. You must associate a message group ID with a message\. If you don't provide a message group ID, the action fails\. If you require a single group of ordered messages, provide the same message group ID for messages sent to the FIFO queue\.

**Receiving messages**  
You can't request to receive messages with a specific message group ID\.  
When receiving messages from a FIFO queue with multiple message group IDs, Amazon SQS first attempts to return as many messages with the same message group ID as possible\. This allows other consumers to process messages with a different message group ID\.  
It is possible to receive up to 10 messages in a single call using the `MaxNumberOfMessages` request parameter of the `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` action\. These messages retain their FIFO order and can have the same message group ID\. Thus, if there are fewer than 10 messages available with the same message group ID, you might receive messages from another message group ID, in the same batch of 10 messages, but still in FIFO order\.

**Retrying multiple times**  
FIFO queues allow the producer or consumer to attempt multiple retries:  
+ If the producer detects a failed `SendMessage` action, it can retry sending as many times as necessary, using the same message deduplication ID\. Assuming that the producer receives at least one acknowledgement before the deduplication interval expires, multiple retries neither affect the ordering of messages nor introduce duplicates\.
+ If the consumer detects a failed `ReceiveMessage` action, it can retry as many times as necessary, using the same receive request attempt ID\. Assuming that the consumer receives at least one acknowledgement before the visibility timeout expires, multiple retries don't affect the ordering of messages\.
+ When you receive a message with a message group ID, no more messages for the same message group ID are returned unless you delete the message or it becomes visible\.