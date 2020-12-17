# Preview: High throughput for FIFO queues<a name="high-throughput-fifo"></a>


|  | 
| --- |
| This feature is offered as a preview for a limited time \(“Preview”\)\. AWS may change the functionality of the Preview at any time\. The Preview is not intended for production workloads, and standard fees apply\. Your use of the Preview is governed by the [AWS Service Terms](https://aws.amazon.com/service-terms/), including the terms on Betas and Previews\. | 

High throughput for [FIFO queues](FIFO-queues.md) provides a high number of transactions per second \(TPS\) for messages in those queues\. For information on throughput quotas, see [Quotas related to messages](quotas-messages.md)\.

You can enable high throughput for any new or existing FIFO queue\. The feature includes three new options when you create and edit FIFO queues:
+ **Enable high throughput FIFO** – Makes higher throughput available for messages in the current FIFO queue\.
+ **Deduplication scope** – Specifies whether deduplication occurs at the queue or message group level\.
+ **FIFO throughput limit** – Specifies whether the throughput quota on messages in the FIFO queue is set at the queue or message group level\.

Currently, this feature is available in the following AWS Regions:


| Region name | Region | 
| --- | --- | 
| US East \(Ohio\) | us\-east\-2 | 
| US East \(N\. Virginia\) | us\-east\-1 | 
| US West \(Oregon\) | us\-west\-2 | 
| Europe \(Ireland\) | eu\-west\-1 | 

**To enable high throughput for a FIFO queue \(console\)**

1. From a supported Region, start [creating](sqs-configure-create-queue.md) or [editing](sqs-configure-edit-queue.md) a FIFO queue\.

1. When specifying options for the queue, choose **Enable high throughput FIFO**\.

   Enabling high throughput FIFO sets the related options as follows:
   + **Deduplication scope** is set to **Message group**, the required setting for using high throughput FIFO\.
   + **FIFO throughput limit** is set to **Per message group ID**, the required setting for using high throughput FIFO\.

   If you change any of the settings required for using high throughput FIFO, standard throughput is in effect for the queue, and deduplication occurs as specified\.

1. Continue specifying all options for the queue\. When you finish, choose **Create queue** or **Save**\.

After creating or editing the FIFO queue, you can [send messages](sqs-using-send-messages.md) to it and [receive and delete messages](sqs-using-receive-delete-message.md), all at a higher TPS\.