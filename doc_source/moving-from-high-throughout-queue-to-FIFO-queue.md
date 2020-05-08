# Moving from an Amazon SQS Standard queue to a FIFO queue<a name="moving-from-high-throughout-queue-to-FIFO-queue"></a>

If you're not setting the `DelaySeconds` parameter on each message, you can move to a FIFO queue by providing a message group ID for every sent message\.

For more information, see [Moving from a Standard queue to a FIFO queue](FIFO-queues.md#FIFO-queues-moving)\.