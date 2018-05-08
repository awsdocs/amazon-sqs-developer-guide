# Getting Started with Amazon SQS<a name="sqs-getting-started"></a>

This section helps you become more familiar with Amazon SQS by showing you how to manage queues and messages using the AWS Management Console\.

## Prerequisites<a name="sqs-prerequisites"></a>

Before you begin, complete the steps in [Setting Up Amazon SQS](sqs-setting-up.md)\.

## Step 1: Create a Queue<a name="step-create-queue"></a>

The first and most common Amazon SQS task is creating queues\. The following example demonstrates how to create and configure a queue\.

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.

1. **Standard** is selected by default\. Choose **FIFO**\.

1. To create your queue with the default parameters, choose **Quick\-Create Queue**\.

   Your new queue is created and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.\.

   The **Queue Type** column helps you distinguish standard queues from FIFO queues at a glance\. For a FIFO queue, the **Content\-Based Deduplication** column displays whether you have enabled [exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-type-content-based-deduplication-columns.png)

   Your queue's **Name**, **URL**, and **ARN** are displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-details-url-arn.png)

## Step 2: Send a Message<a name="step-send-message"></a>

After you create your queue, you can send a message to it\. The following example demonstrates sending a message to an existing queue\.

1. From the queue list, select the queue that you've created\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Send a Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The **Send a Message to *QueueName*** dialog box is displayed\.

   The following example shows the **Message Group ID** and **Message Deduplication ID** parameters specific to FIFO queues \([content\-based deduplication](FIFO-queues.md#FIFO-queues-exactly-once-processing) is disabled\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-dialog-box.png)

1. To send a message to a FIFO queue, type the **Message Body**, the **Message Group ID** `MyMessageGroupId1234567890`, and the **Message Deduplication ID** `MyMessageDeduplicationId1234567890`, and then choose **Send Message**\. For more information, see [FIFO Delivery Logic](FIFO-queues.md#FIFO-queues-understanding-logic)\.
**Note**  
The message group ID is always required\. However, if content\-based deduplication is enabled, the message deduplication ID is optional\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-button-fifo.png)

   Your message is sent and the **Send a Message to *QueueName*** dialog box is displayed, showing the attributes of the sent message\.

   The following example shows the **Sequence Number** attribute specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-message-attributes.png)

1. Choose **Close**\.

## Step 3: Receive and Delete Your Message<a name="step-receive-delete-message"></a>

After you send a message into a queue, you can consume it \(retrieve it from the queue\)\. When you request a message from a queue, you can't specify which message to get\. Instead, you specify the maximum number of messages \(up to 10\) that you want to get\.

The following example demonstrates receiving and deleting a message\.

1. From the queue list, select the queue that you have created\.

1. From **Queue Actions**, select **View/Delete Messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-view-delete-messages.png)

   The **View/Delete Messages in *QueueName*** dialog box is displayed\.
**Note**  
The first time you take this action, an information screen is displayed\. To hide the screen, check the **Don't show this again** checkbox\.

1. Choose **Start Polling for messages\.**  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-start-polling-for-messages.png)

   Amazon SQS begins to poll the messages in the queue\. The dialog box displays a message from the queue\. A progress bar at the bottom of the dialog box displays the status of the message's visibility timeout\.

   The following example shows the **Message Group ID**, **Message Deduplication ID**, and **Sequence Number** columns specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-polling-process.png)

1. *Before* the visibility timeout expires, select the message that you want to delete and then choose **Delete *1* Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-select-message-to-delete.png)

   The **Delete Messages** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-confirm-deleting-messages.png)

1. Confirm that the message you want to delete is checked and choose **Yes, Delete Checked Messages**\.

   The selected message is deleted\.

   When the progress bar is filled in, the [visibility timeout](sqs-visibility-timeout.md) expires and the message becomes visible to consumers\.

1. Select **Close**\.

## Step 4: Delete Your Queue<a name="step-delete-queue"></a>

If you don't use an Amazon SQS queue \(and don't foresee using it in the near future\), it is a best practice to delete it from Amazon SQS\. The following example demonstrates deleting a queue\.

1. From the queue list, select the queue that you have created\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Delete Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-deleting-queue-delete-queue.png)

   The **Delete Queues** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-deleting-queue-delete-queue-dialog-box.png)

1. Choose **Yes, Delete Queue**\.

   The queue is deleted\.

## Next Steps<a name="sqs-next-steps-getting-started"></a>

Now that you've created a queue and learned how to send, receive, and delete messages and how to delete a queue, you might want to try the following:
+ [Enable server\-side encryption \(SSE\) for a new queue](sqs-create-queue-sse.md) or [configure SSE for an existing queue\.](sqs-configure-sse-existing-queue.md)
+ [Add permissions to a queue\.](sqs-add-permissions.md)
+ [Add, update, or remove tags for a queue\.](sqs-add-update-remove-tag-queue.md)
+ [Configure long polling for a queue\.](sqs-configure-long-polling-for-queue.md)
+ [Send a message with attributes\.](sqs-send-message-with-attributes.md)
+ [Configure a dead\-letter queue\.](sqs-configure-dead-letter-queue.md)
+ [Configure visibility timeout for a queue\.](sqs-configure-visibility-timeout-queue.md)
+ [Configure a delay queue\.](sqs-configure-delay-queue.md)
+ [Subscribe a queue to an Amazon SNS topic\.](sqs-subscribe-queue-sns-topic.md)
+ [Purge a queue\.](sqs-purge-queue.md)
+ Learn more about Amazon SQS workflows and processes: Read [How Queues Work](sqs-how-it-works.md), [Best Practices](sqs-best-practices.md), and [Limits](sqs-limits.md)\. You can also explore the [Amazon SQS Articles & Tutorials](https://aws.amazon.com/articles/Amazon-SQS?browse=1)\. If you ever have any questions, browse the [Amazon SQS FAQs](https://aws.amazon.com/sqs/faqs/) or participate in the [Amazon SQS Developer Forums](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.
+ Learn how to interact with Amazon SQS programmatically: Read [Working with APIs](sqs-making-api-requests.md) and explore the [Sample Code and Libraries](https://aws.amazon.com/code/Amazon-SQS?browse=1) and the developer centers:
  + [Java](https://aws.amazon.com/java/)
  + [JavaScript](https://aws.amazon.com/javascript/)
  + [PHP](https://aws.amazon.com/php/)
  + [Python](https://aws.amazon.com/python/)
  + [Ruby](https://aws.amazon.com/ruby/)
  + [Windows & \.NET](https://aws.amazon.com/net/)
+ Learn about keeping an eye on costs and resources in the [Monitoring, Logging, and Automating Amazon SQS Queues](sqs-monitoring-logging.md) section\.
+ Learn about protecting your data and access to it in the [Security](sqs-security.md) section\.