# Message ordering<a name="FIFO-queues-message-order"></a>

The FIFO queue improves upon and complements the [standard queue](standard-queues.md)\. The most important features of this queue type are [*FIFO \(First\-In\-First\-Out\) delivery*](FIFO-queues-understanding-logic.md) and *[exactly\-once processing](FIFO-queues-exactly-once-processing.md)*:
+ The order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it\.
+ Duplicates aren't introduced into the queue\.

In addition, FIFO queues support *message groups* that allow multiple ordered message groups within a single queue\. There is no quota to the number of message groups within a FIFO queue\.