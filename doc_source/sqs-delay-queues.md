# Amazon SQS Delay Queues<a name="sqs-delay-queues"></a>

Delay queues let you postpone the delivery of new messages to a queue for a number of seconds\. If you create a delay queue, any message that you send to that queue remains invisible to consumers for the duration of the delay period\. To create a delay queue, set the `DelaySeconds` attribute of the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` action to any value between `0` and `900` \(15 minutes\)\. To change an existing queue into a delay queue use the `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action to set the queue's `DelaySeconds` attribute\.

**Note**  
For standard queues, the per\-queue delay setting *isn't retroactive*: If you change the `DelaySeconds` attribute, it doesn't affect the delay of messages already in the queue\.  
For FIFO queues, the per\-queue delay setting *is retroactive*: If you change the `DelaySeconds` attribute, it affects the delay of messages already in the queue\.

Delay queues are similar to [visibility timeouts](sqs-visibility-timeout.md) because both features make messages unavailable to consumers for a specific period of time\. The difference between the two is that, for delay queues, a message is hidden *when it is first added to queue*, whereas for visibility timeouts a message is hidden *only after it is consumed from the queue*\. The following figure illustrates the relationship between delay queues and visibility timeouts\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/Delay_Queues.png)

To set delay seconds on *individual messages*, rather than on an entire queue, use [message timers](sqs-message-timers.md) to allow Amazon SQS to use the message timer's `DelaySeconds` value instead of the delay queue's `DelaySeconds` value\.

## Creating Delay Queues with the AWS Management Console<a name="sqs-delay-queues-console"></a>

You can create a delay queue using the AWS Management Console by setting a **Delivery Delay** to a value greater than `0`\.

**To create a delay queue with the AWS Management Console**

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue.png)

1. In the **Create New Queue** dialog box, type your **Queue Name**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue_Dialog.png)

1. For **Delivery Delay**, type a positive integer value\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue_Dialog_Delay.png)

1. Choose **Create Queue**\.

You can use the AWS Management Console to change the **Delivery Delay** setting for an existing queue by selecting the **Configure Queue** action with an existing queue selected\.

**To set a new delivery delay value for an existing queue**

1. Select an existing queue and then from the **Queue Actions** drop\-down box select **Configure Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-queue.png)

1. Change the **Delivery Delay** value to a positive integer\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Configure_Queue_Dialog_Delay.png)

1. Choose **Save Changes**\.