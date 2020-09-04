# Creating an Amazon SQS queue \(console\)<a name="sqs-configure-create-queue"></a>

You can use the Amazon SQS console to create [standard queues](standard-queues.md) and [FIFO queues](FIFO-queues.md)\. The console provides default values for all fields except for the queue name\. 

**To create an Amazon SQS queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create queue**\.

1. On the **Create queue** page, ensure that you set the correct region\.

1. The **Standard** queue type is selected by default\. To create a FIFO queue, choose **FIFO**\.

   You can't change the queue type after you create a queue\. 

1.  Enter a **Name** for your queue\. The name of a FIFO queue must end with the `.fifo` suffix\.

1. To create a queue using all of the default settings, scroll to the end of the page and choose **Create queue**\. After you create the queue, you can [edit](sqs-configure-edit-queue.md) all of the queue configuration settings \(except the queue type\)\. 

1. \(Optional\) The console sets default values for the queue configuration parameters\. You can set new values for the following parameters\. For additional information, see [Configuration parameters](sqs-configure-queue-parameters.md)\. 

   1. For **Visibility timeout **, enter the duration and units\. The range is 0 seconds to 12 hours\. The default value is 30 seconds\.

   1. For **Message retention period**, enter the duration and units\. The range is 1 minute to 14 days\. The default value is 4 days\.

   1. For **Delivery delay**, enter the duration and units\. The range is 0 seconds to 15 minutes\. The default value is 0 seconds\.

   1. For **Maximum message size**, enter a value\. The range is 1 byte to 256 KB\. The default value is 256 KB\. 

   1. For a standard queue, enter a value for **Receive message wait time**\. The range is 0 to 20 seconds\. The default value is 0 seconds, which sets [short polling](sqs-short-and-long-polling.md)\. Any non\-zero value sets long polling\.

   1. For a FIFO queue, choose **Content\-based deduplication** to enable content\-based deduplication\. The default setting is disabled\. 

1. \(Optional\) Define an [Access policy](sqs-creating-custom-policies-access-policy-examples.md)\. The access policy defines the accounts, users, and roles that can access the queue\. The access policy also defines the actions \(such as `SendMessage`, `ReceiveMessage`, or `DeleteMessage`\) that the users can access\. The default policy allows only the queue owner to send and receive messages\. 

   You can configure basic and advanced settings for the policy\. 

   1. In the basic settings, you configure who can send messages to the queue, and who can receive messages from the queue\. The read\-only JSON panel displays the resulting access policy for the queue\. 

   1. In the advanced settings, you modify the JSON access policy directly\. This allows you to specify a custom set of actions that each principal \(account, user, or role\) can perform\. 

1. \(Optional\) Add [Encryption](sqs-configure-sse-existing-queue.md) to the queue\.

1. \(Optional\) Add a [Dead\-letter queue](sqs-configure-dead-letter-queue.md) to receive undeliverable messages\.

1. \(Optional\) Add [Tags](sqs-configure-tag-queue.md) to the queue\.

1. When you finish configuring the queue, choose **Create queue**\. Amazon SQS creates the queue and displays the queue's **Details** page\.

Amazon SQS propagates information about the new queue across the system\. Because Amazon SQS is a distributed system, you might experience a slight delay before the queue is displayed on the **Queues** page\. 

After creating a queue, you can [send messages](sqs-using-send-messages.md) to it, and [receive and delete messages](sqs-using-receive-delete-message.md)\.