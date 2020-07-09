# Sending messages to a queue \(console\)<a name="sqs-using-send-messages"></a>

After you create your queue, you can send messages to it\.

**To send a message \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose a queue\.

1. From **Actions**, choose **Send and receive messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The console displays the **Send and receive messages** page\.

1. In the **Message body**, enter the message text\.

1. For a First\-In\-First\-Out \(FIFO\) queue, enter a **Message group ID**\. For more information, see [FIFO delivery logic](FIFO-queues.md#FIFO-queues-understanding-logic)\.

1. \(Optional\) For a FIFO queue, you can enter a **Message deduplication ID**\. If you enabled content\-based deduplication for the queue, the message deduplication ID isn't required\. For more information, see [FIFO delivery logic](FIFO-queues.md#FIFO-queues-understanding-logic)\. 

1. \(Optional\) For a standard queue, you can enter a value for **Delivery delay** and choose the units\. For example, enter `60` and choose **seconds**\. FIFO queues don't support timers on individual messages\. For more information, see [Amazon SQS message timers](sqs-message-timers.md)\.

1. Choose **Send message**\.

   When your message is sent, the console displays a success message\. Choose **View details** to display information about the sent message\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-success.png)