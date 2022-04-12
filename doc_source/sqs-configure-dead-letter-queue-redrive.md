# Configuring a dead\-letter queue redrive \(console\)<a name="sqs-configure-dead-letter-queue-redrive"></a>

You can configure a *dead\-letter queue redrive* to move standard unconsumed messages out of an existing dead\-letter queue back to their source queues\. For more information about dead letter queue redrive, see [Moving messages out of a dead\-letter queue](sqs-dead-letter-queues.md#sqs-dead-letter-queues-redrive)\.

**To configure a dead\-letter queue redrive for an existing standard queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. Choose the name of queue that you have configured as a [dead\-letter queue](sqs-configure-dead-letter-queue.md)\.

1. Choose **Start DLQ redrive**\.

1. Under **Redrive configuration**, for **Message destination**, do either of the following:
   + To redrive messages to their source queue, choose **Redrive to source queue\(s\)**\.
   + To redrive messages to another queue, choose **Redrive to custom destination**\. Then, enter the Amazon Resource Name \(ARN\) of an existing destination queue\.

1. Under **Velocity control settings**, choose one of the following:
   + **System optimized** \- Redrive dead\-letter queue messages at the maximum number of messages per second\.
   + **Custom max velocity** \- Redrive dead\-letter queue messages with a custom maximum rate of messages per second\. The maximum allowed rate is 500 messages per second\.

1. When you finish configuring the dead\-letter queue redrive, choose **Redrive messages**\.
**Important**  
Amazon SQS supports dead\-letter queue redrive only for standard queues in the Amazon SQS console\.  
Amazon SQS doesn't support filtering and modifying messages while redriving them from the dead\-letter queue\.  
A dead\-letter queue redrive task can run a maximum of 36 hours\. Amazon SQS supports a maximum of 100 active redrive tasks per account\.  
The redrive task resets the retention period\. A new `messageID` and `enqueueTime` are assigned to redriven messages\.

1. If you want to cancel the message redrive task, on the **Details** page for your queue, choose **Cancel DLQ redrive**\. When canceling an in progress message redrive, any messages that have already been successfully moved to their move destination queue will remain in the destination queue\.