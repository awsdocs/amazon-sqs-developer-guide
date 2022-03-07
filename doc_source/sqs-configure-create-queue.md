# Creating an Amazon SQS queue \(console\)<a name="sqs-configure-create-queue"></a>

You can use the Amazon SQS console to create [standard queues](standard-queues.md) and [FIFO queues](FIFO-queues.md)\. The console provides default values for all settings except for the queue name\.

**Important**  
Do not add personally identifiable information \(PII\) or other confidential or sensitive information in queue names\. Queue names are accessible to many Amazon Web Services, including billing and CloudWatch logs\. Queue names are not intended to be used for private or sensitive data\.

**To create an Amazon SQS queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create queue**\.

1. For **Type**, the **Standard** queue type is set by default\. To create a FIFO queue, choose **FIFO**\.
**Note**  
You can't change the queue type after you create the queue\.

1.  Enter a **Name** for your queue\. The name of a FIFO queue must end with the `.fifo` suffix\.

1. \(Optional\) The console sets default values for the queue [configuration parameters](sqs-configure-queue-parameters.md)\. Under **Configuration**, you can set new values for the following parameters:

   1. For **Visibility timeout **, enter the duration and units\. The range is from 0 seconds to 12 hours\. The default value is 30 seconds\.

   1. For **Message retention period**, enter the duration and units\. The range is from 1 minute to 14 days\. The default value is 4 days\.

   1. For **Delivery delay**, enter the duration and units\. The range is from 0 seconds to 15 minutes\. The default value is 0 seconds\.

   1. For **Maximum message size**, enter a value\. The range is from 1 KB to 256 KB\. The default value is 256 KB\. 

   1. For **Receive message wait time**, enter a value\. The range is from 0 to 20 seconds\. The default value is 0 seconds, which sets [short polling](sqs-short-and-long-polling.md)\. Any non\-zero value sets long polling\.

   1. For a FIFO queue, choose **Enable content\-based deduplication** to enable content\-based deduplication\. The default setting is disabled\. 

   1. \(Optional\) For a FIFO queue, to enable higher throughput for sending and receiving messages in the queue, choose **Enable high throughput FIFO**\.

      Choosing this option changes the related options \(**Deduplication scope** and **FIFO throughput limit**\) to the required settings for enabling high throughput for FIFO queues\. If you change any of the settings required for using high throughput FIFO, normal throughput is in effect for the queue, and deduplication occurs as specified\. For more information, see [High throughput for FIFO queues](high-throughput-fifo.md) and [Quotas related to messages](quotas-messages.md)\.

   1. For **Redrive allow policy**, choose **Allow all** \(the default\), **By queue** or **Deny all**\. When choosing **By queue**, specify a list of up to 10 source queues by the Amazon Resource Name \(ARN\)\.

1. \(Optional\) Define an **Access policy**\. The [access policy](sqs-creating-custom-policies-access-policy-examples.md) defines the accounts, users, and roles that can access the queue\. The access policy also defines the actions \(such as `SendMessage`, `ReceiveMessage`, or `DeleteMessage`\) that the users can access\. The default policy allows only the queue owner to send and receive messages\.

   To define the access policy, do one of the following:
   + Choose **Basic** to configure who can send messages to the queue and who can receive messages from the queue\. The console creates the policy based on your choices and displays the resulting access policy in the read\-only JSON panel\.
   + Choose **Advanced** to modify the JSON access policy directly\. This allows you to specify a custom set of actions that each principal \(account, user, or role\) can perform\.

1. \(Optional\) To configure [encryption](sqs-configure-sse-existing-queue.md) for the queue, expand **Encryption**\.

1. \(Optional\) To configure a [dead\-letter queue](sqs-configure-dead-letter-queue.md) to receive undeliverable messages, expand **Dead\-letter queue**\.

1. \(Optional\) To add [tags](sqs-configure-tag-queue.md) to the queue, expand **Tags**\.

1. Choose **Create queue**\. Amazon SQS creates the queue and displays the queue's **Details** page\.

Amazon SQS propagates information about the new queue across the system\. Because Amazon SQS is a distributed system, you might experience a slight delay before the console displays the queue on the **Queues** page\.

After creating a queue, you can [send messages](sqs-using-send-messages.md) to it, and [receive and delete messages](sqs-using-receive-delete-message.md)\. You can also [edit](sqs-configure-edit-queue.md) any of the queue configuration settings except the queue type\.