# Amazon SQS Message Timers<a name="sqs-message-timers"></a>

Amazon SQS message timers allow you to specify an initial invisibility period for a message that you add to a queue\. For example, if you send a message with the `DelaySeconds` parameter set to `45`, the message isn't visible to consumers for the first `45` seconds during which the message stays in the queue\. The default value for `DelaySeconds` is `0`\. 

**Note**  
FIFO queues don't support timers on individual messages\.  
A message is considered to be *in flight* after it's received from a queue by a consumer, but not yet deleted from the queue\.  
For standard queues, there can be a maximum of 120,000 inflight messages per queue\. If you reach this limit, Amazon SQS returns the `OverLimit` error message\. To avoid reaching the limit, you should delete messages from the queue after they're processed\. You can also increase the number of queues you use to process your messages\.

To set a delay period that applies to all messages in a queue, use [delay queues](sqs-delay-queues.md)\. A message timer setting for an individual message overrides any `DelaySeconds` value that applies to the entire delay queue\. 


+ [Creating Message Timers Using the Console](#sqs-message-timers-console)
+ [Creating Message Timers Using the Query API](#sqs-message-timers-query-api)

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

## Creating Message Timers Using the Query API<a name="sqs-message-timers-query-api"></a>

The following Query API example applies a 45\-second initial visibility delay for a single message sent with `SendMessage`\.

The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue/
?Action=SendMessage
&MessageBody=This+is+a+test+message
&DelaySeconds=45
&Expires=2015-12-18T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Queue names and queue URLs are case\-sensitive\.

You can also use the Query API `SendMessageBatch` action to send up to 10 messages with message timers\. You can assign a different `DelaySeconds` value to each message or assign no value at all\. If you don't set a value for DelaySeconds, the message might still be subject to a delay if you're adding the message to a delay queue\. For more information about delay queues, see [Amazon SQS Delay Queues](sqs-delay-queues.md)\. The following example uses `SendMessageBatch` to send three messages: one message without a message timer and two messages with different values for `DelaySeconds`\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue/
?Action=SendMessageBatch
&SendMessageBatchRequestEntry.1.Id=test_msg_no_message_timer
&SendMessageBatchRequestEntry.1.MessageBody=test%20message%20body%201
&SendMessageBatchRequestEntry.2.Id=test_msg_delay_45_seconds
&SendMessageBatchRequestEntry.2.MessageBody=test%20message%20body%202
&SendMessageBatchRequestEntry.2.DelaySeconds=45
&SendMessageBatchRequestEntry.3.Id=test_msg_delay_2_minutes
&SendMessageBatchRequestEntry.3.MessageBody=test%20message%20body%203
&SendMessageBatchRequestEntry.3.DelaySeconds=120
&Expires=2015-12-18T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```