# Getting started with Amazon SQS<a name="sqs-getting-started"></a>

This section helps you become more familiar with Amazon SQS by showing you how to manage queues and messages using the Amazon SQS console\.

**Topics**
+ [Prerequisites](#sqs-prerequisites)
+ [Step 1: Create a queue](#step-create-queue)
+ [Step 2: Send a message](#step-send-message)
+ [Step 3: Receive and delete your message](#step-receive-delete-message)
+ [Step 4: Delete your queue](#step-delete-queue)
+ [Next steps](#sqs-next-steps-getting-started)

## Prerequisites<a name="sqs-prerequisites"></a>

Before you begin, complete the steps in [Setting up Amazon SQS](sqs-setting-up.md)\.

## Step 1: Create a queue<a name="step-create-queue"></a>

The first and most common Amazon SQS task is creating queues\. This procedure shows how to create and configure a FIFO queue\.

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create queue**\.

1. On the **Create queue** page, ensure that you set the correct region\.

1. The **Standard** queue type is selected by default\. Choose **FIFO**\.

   You can't change the queue type after you create a queue\. 

1.  Enter a **Name** for your queue\. The name of a FIFO queue must end with the `.fifo` suffix\.

1. To create your queue with the default parameters, scroll to the bottom and choose **Create Queue**\. Amazon SQS creates the queue and displays the queue's **Details** page\. 

   Amazon SQS propagates information about the new queue across the system\. Because Amazon SQS is a distributed system, you may experience a slight delay before the queue is displayed on the **Queues** page\. 

## Step 2: Send a message<a name="step-send-message"></a>

After you create your queue, you can send a message to it\.

1. From the left navigation pane, choose **Queues**\. From the queue list, select the queue that you created\.

1. From **Actions**, choose **Send and receive messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The console displays the **Send and receive messages** page\.

1. Enter text in the **Message body**

1. Enter a **Message group id** for the queue\. For more information, see [FIFO delivery logic](FIFO-queues.md#FIFO-queues-understanding-logic)\. 

1. \(Optional\) Enter a **Message deduplication id**\. If you enable content\-based deduplication, the message deduplication ID is not required\. For more information, see [FIFO delivery logic](FIFO-queues.md#FIFO-queues-understanding-logic)\.

1. Choose **Send message**\.

   Your message is sent and the console displays a success message\. Choose **View details** to display information about the sent message\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-success.png)

## Step 3: Receive and delete your message<a name="step-receive-delete-message"></a>

After you send a message to a queue, you can retrieve the message from the queue\. When you request messages from a queue, you can't specify which message to retrieve\. Instead, you specify the maximum number of messages \(up to 10\) that you want to retrieve\.

1. From the **Queues** page, select a queue\.

1. From **Queue Actions**, select **Send and receive messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The console displays the **Send and receive messages** page\.

1. Choose **Poll for messages\.**

   Amazon SQS begins to poll servers to find messages in the queue\. The progress bar on the right side of the **Receive messages** section displays the polling duration\.

   The **Messages** section displays a list of the received messages\. For each message, the list displays the message ID, sent date, size, and receive count\.

1. To delete messages, select the messages that you want to delete and then choose **Delete**\.

1. In the **Delete Messages** dialog box, choose **Delete**\.

## Step 4: Delete your queue<a name="step-delete-queue"></a>

1. From the queue list, select the queue that you have created\.

1. From the **Queues** page, select the queue to delete\. 

1. Choose **Delete queue**\.

   The console displays the **Delete queue** dialog box\.

1. In the **Delete queue** dialog box, confirm the deletion by entering **delete**\.

1. Choose **Delete**\.

## Next steps<a name="sqs-next-steps-getting-started"></a>

Now that you've created a queue and learned how to send, receive, and delete messages and how to delete a queue, you might want to try the following:
+  [Configure queues, including SSE and other features](sqs-configuring.md)\.
+ [Send a message with attributes\.](sqs-using-send-message-with-attributes.md)
+ [Send a message from a VPC\.](sqs-sending-messages-from-vpc.md)
+ Learn more about Amazon SQS workflows and processes: Read [How Queues Work](sqs-how-it-works.md), [Best Practices](sqs-best-practices.md), and [Quotas](sqs-quotas.md)\. You can also explore the [Amazon SQS Articles & Tutorials](https://aws.amazon.com/articles/Amazon-SQS?browse=1)\. If you ever have any questions, browse the [Amazon SQS FAQs](https://aws.amazon.com/sqs/faqs/) or participate in the [Amazon SQS Developer Forums](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.
+ Learn how to interact with Amazon SQS programmatically: Read [Working with APIs](sqs-making-api-requests.md) and explore the [Sample Code and Libraries](https://aws.amazon.com/code/Amazon-SQS?browse=1) and the developer centers:
  + [Java](https://aws.amazon.com/java/)
  + [JavaScript](https://aws.amazon.com/javascript/)
  + [PHP](https://aws.amazon.com/php/)
  + [Python](https://aws.amazon.com/python/)
  + [Ruby](https://aws.amazon.com/ruby/)
  + [Windows & \.NET](https://aws.amazon.com/net/)
+ Learn about keeping an eye on costs and resources in the [Automating and troubleshooting Amazon SQS queues](sqs-automating-troubleshooting.md) section\.
+ Learn about protecting your data and access to it in the [Security](sqs-security.md) section\.