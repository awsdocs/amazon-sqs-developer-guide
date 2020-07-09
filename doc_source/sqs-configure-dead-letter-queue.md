# Configuring a dead\-letter queue \(console\)<a name="sqs-configure-dead-letter-queue"></a>

A *dead\-letter queue* is a queue that one or more source queues can use for messages that are not consumed successfully\. For more information, see [Amazon SQS dead\-letter queues](sqs-dead-letter-queues.md)\.

Amazon SQS does *not* create the dead\-letter queue automatically\. You must first create the queue before using it as a dead\-letter queue\.\.

The dead\-letter queue of a FIFO queue must also be a FIFO queue\. Similarly, the dead\-letter queue of a standard queue must also be a standard queue\.

When you [create](sqs-configure-create-queue.md) or [edit](sqs-configure-edit-queue.md) a queue, you can configure a dead\-letter queue\.

**To configure a dead\-letter queue for an existing queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\. 

1. Choose a queue and choose **Edit**\. 

1. Scroll to the **Dead\-letter queue** section and choose **Enabled**\.

1. Choose the Amazon Resource Name \(ARN\) of an existing **Dead Letter Queue** that you want to associate with this source queue\.

1. To configure the number of times that a message can be received before being sent to a dead\-letter queue, set **Maximum receives** to a value between 1 and 1,000\.

1. When you finish configuring the dead\-letter queue, choose **Save**\.

   After you save the queue, the console displays the **Details** page for your queue\. On the **Details** page, the **Dead\-letter queue** tab displays the **Maximum Receives** and **Dead Letter Queue** ARN in the **Dead\-letter queue**\.