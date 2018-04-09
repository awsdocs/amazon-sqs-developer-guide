# Amazon SQS Message Timers<a name="sqs-message-timers"></a>

Amazon SQS message timers allow you to specify an initial invisibility period for a message that you add to a queue\. For example, if you send a message with the `DelaySeconds` parameter set to `45`, the message isn't visible to consumers for the first `45` seconds during which the message stays in the queue\. The default value for `DelaySeconds` is `0`\. 

**Note**  
FIFO queues don't support timers on individual messages\.  
An Amazon SQS message has three basic states: 1\. Sent to a queue by a producer, 2\. Received from the queue by a consumer, and 3\. Deleted from the queue\. A message is considered to be *in flight* after it is received from a queue by a consumer, but not yet deleted from the queue \(that is, between states 2 and 3\)\. There is no limit to the number of messages in a queue which are between states 1 and 2\.  
For standard queues, there can be a maximum of 120,000 inflight messages in a queue\. If you reach this limit, Amazon SQS returns the `OverLimit` error message\. To avoid reaching the limit, you should delete messages from the queue after they're processed\. You can also increase the number of queues you use to process your messages\.

To set a delay period that applies to all messages in a queue, use [delay queues](sqs-delay-queues.md)\. A message timer setting for an individual message overrides any `DelaySeconds` value that applies to the entire delay queue\. 


+ [Creating Message Timers Using the Console](#sqs-message-timers-console)

## Creating Message Timers Using the Console<a name="sqs-message-timers-console"></a>

**To send a message with a message timer using the AWS Management Console**

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Select_Queue.png)

1. From the **Queue Actions** drop\-down list, select **Send a Message**\.
**Note**  
The **Queue Actions** drop\-down list is available only if a queue is selected\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Send_Message.png)

1. In the **Send a Message to MyQueue** dialog box, type a message\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_SendMessage_Dialog_Message_Timer_Text.png)

1. In the **Delay delivery of this message by** text box, enter a delay value \(for example, `30`\) \.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_SendMessage_Dialog_Message_Timer_Delay.png)

1. Choose **Send Message**\.

1. In the **Send a Message to MyQueue** confirmation box, choose **Close**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Send_Message_Dialog_Conf.png)