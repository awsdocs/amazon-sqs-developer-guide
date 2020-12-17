# Configuring queue parameters \(console\)<a name="sqs-configure-queue-parameters"></a>


****  

|  | 
| --- |
| High throughput for Amazon SQS FIFO queues is in preview release and is subject to change\. For more information, see [Preview: High throughput for FIFO queues](high-throughput-fifo.md)\. | 

When you [create](sqs-configure-create-queue.md) or [edit](sqs-configure-edit-queue.md) a queue, you can configure the following parameters:
+ **Visibility timeout** – The length of time that a message received from a queue \(by one consumer\) won't be visible to the other message consumers\. For more information, see [Visibility timeout](sqs-visibility-timeout.md)\. 
**Note**  
Using the console to configure the visibility timeout configures the timeout value for all of the messages in the queue\. To configure the timeout for single or multiple messages, you must use one of the AWS SDKs\. 
+ **Message retention period** – The amount of time that Amazon SQS retains messages that remain in the queue\. By default, the queue retains messages for four days\. You can configure a queue to retain messages for up to 14 days\. For more information, see [Message retention period](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)\.
+ **Delivery delay** – The amount of time that Amazon SQS will delay before delivering a message that is added to the queue\. For more information, see [Delivery delay](sqs-delay-queues.md)\.
+ **Maximum message size** – The maximum message size for this queue\. For more information, see [Maximum message size](sqs-s3-messages.md)\.
+ **Receive message wait time** – The maximum amount of time that Amazon SQS waits for messages to become available after the queue gets a receive request\. For more information, see [Amazon SQS short and long polling](sqs-short-and-long-polling.md)\.
+ **Enable content\-based deduplication** – Amazon SQS can automatically create deduplication IDs based on the body of the message\. For more information, see [Amazon SQS FIFO \(First\-In\-First\-Out\) queues](FIFO-queues.md)\.
+ **Enable high throughput FIFO** – Use to enable high throughput for messages in the queue\. This option is currently in preview release\. Choosing this option changes the related preview options \(**Deduplication scope** and **FIFO throughput limit**\) to the required settings for enabling high throughput\. For more information, see [Preview: High throughput for FIFO queues](high-throughput-fifo.md) and [Quotas related to messages](quotas-messages.md)\.



**To configure queue parameters for an existing queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\. Choose a queue and choose **Edit**\. 

1. Scroll to the **Configuration** section\.

1. For **Visibility timeout **, enter the duration and units\. The range is 0 seconds to 12 hours\. The default value is 30 seconds\.

1. For **Message retention period**, enter the duration and units\. The range is 1 minute to 14 days\. The default value is 4 days\.

1. For **Delivery delay**, enter the duration and units\. The range is 0 seconds to 15 minutes\. The default value is 0 seconds\.

1. For **Maximum message size**, enter a value\. The range is 1 KB to 256 KB\. The default value is 256 KB\. 

1. For a standard queue, enter a value for **Receive message wait time**\. The range is 0 to 20 seconds\. The default value is 0 seconds, which sets [short polling](sqs-short-and-long-polling.md)\. Any non\-zero value sets long polling\.

1. For a FIFO queue, choose **Enable content\-based deduplication** to enable content\-based deduplication\. The default setting is disabled\. 

1. \(Optional\) For a FIFO queue, to enable high throughput for sending and receiving messages in the queue, choose **Enable high throughput FIFO** \(preview release\)\.

1. When you finish configuring the queue parameters, choose **Save**\.