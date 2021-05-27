# High throughput for FIFO queues<a name="high-throughput-fifo"></a>

High throughput for [FIFO queues](FIFO-queues.md) supports a higher number of requests per API, per second\. To increase the number of requests in high throughput for FIFO queues, you can increase the number of message groups you use\. Each message group supports 300 requests per second\. For information on per\-queue quotas with high throughput for FIFO quotas, see [Quotas related to messages](quotas-messages.md) and [Partitions and data distribution for high throughput for SQS FIFO queues](partitions-and-data-distribution.md)\.

You can enable high throughput for any new or existing FIFO queue\. The feature includes three new options when you create and edit FIFO queues:
+ **Enable high throughput FIFO** – Makes higher throughput available for messages in the current FIFO queue\.
+ **Deduplication scope** – Specifies whether deduplication occurs at the queue or message group level\.
+ **FIFO throughput limit** – Specifies whether the throughput quota on messages in the FIFO queue is set at the queue or message group level\.

**To enable high throughput for a FIFO queue \(console\)**

1. Start [creating](sqs-configure-create-queue.md) or [editing](sqs-configure-edit-queue.md) a FIFO queue\.

1. When specifying options for the queue, choose **Enable high throughput FIFO**\.

   Enabling high throughput for FIFO queues sets the related options as follows:
   + **Deduplication scope** is set to **Message group**, the required setting for using high throughput for FIFO queues\.
   + **FIFO throughput limit** is set to **Per message group ID**, the required setting for using high throughput for FIFO queues\.

   If you change any of the settings required for using high throughput for FIFO queues, normal throughput is in effect for the queue, and deduplication occurs as specified\.

1. Continue specifying all options for the queue\. When you finish, choose **Create queue** or **Save**\.

After creating or editing the FIFO queue, you can [send messages](sqs-using-send-messages.md) to it and [receive and delete messages](sqs-using-receive-delete-message.md), all at a higher TPS\.