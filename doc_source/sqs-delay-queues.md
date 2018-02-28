# Amazon SQS Delay Queues<a name="sqs-delay-queues"></a>


+ [Creating Delay Queues with the AWS Management Console](#sqs-delay-queues-console)
+ [Creating Delay Queues with the Query API](#sqs-delay-queues-query-api)

Delay queues let you postpone the delivery of new messages in a queue for the specified number of seconds\. If you create a delay queue, any message that you send to that queue is invisible to consumers for the duration of the delay period\. You can use the `CreateQueue` action to create a delay queue by setting the `DelaySeconds` attribute to any value between `0` and `900` \(15 minutes\)\. You can also change an existing queue into a delay queue using the `SetQueueAttributes` action to set the queue's `DelaySeconds` attribute\.

**Note**  
For standard queues, the per\-queue delay setting *isn't retroactive*: If you change the `DelaySeconds` attribute, it doesn't affect the delay of messages already in the queue\.  
For FIFO queues, the per\-queue delay setting *is retroactive*: If you change the `DelaySeconds` attribute, it affects the delay of messages already in the queue\.

Delay queues are similar to visibility timeouts because both features make messages unavailable to consumers for a specific period of time\. The difference between delay queues and visibility timeouts is that for delay queues a message is hidden when it's first added to queue, whereas for visibility timeouts a message is hidden only after a message is consumed from the queue\. The following figure illustrates the relationship between delay queues and visibility timeouts\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/Delay_Queues.png)

**Note**  
A message is considered to be *in flight* after it's received from a queue by a consumer, but not yet deleted from the queue\.  
For standard queues, there can be a maximum of 120,000 inflight messages per queue\. If you reach this limit, Amazon SQS returns the `OverLimit` error message\. To avoid reaching the limit, you should delete messages from the queue after they're processed\. You can also increase the number of queues you use to process your messages\.  
For FIFO queues, there can be a maximum of 20,000 inflight messages per queue\. If you reach this limit, Amazon SQS returns no error messages\.

To set delay seconds on individual messages, rather than for an entire queue, use message timers\. If you send a message with a message timer, Amazon SQS uses the message timer's delay seconds value instead of the delay queue's delay seconds value\. For more information, see [Amazon SQS Message Timers](sqs-message-timers.md)\.

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

## Creating Delay Queues with the Query API<a name="sqs-delay-queues-query-api"></a>

The following Query API example calls the `CreateQueue` action to create a delay queue that hides each message from consumers for the first 45 seconds that the message is in the queue\.

The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

```
https://sqs.us-east-2.amazonaws.com/
?Action=CreateQueue
&QueueName=MyQueue
&Attribute.1.Name=DelaySeconds
&Attribute.1.Value=45
&Expires=2015-12-20T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Queue names and queue URLs are case\-sensitive\.

You can also change an existing queue into a delay queue by changing the DelaySeconds attribute from its default value of 0 to a positive integer value that is less than or equal to 900\. The following example calls `SetQueueAttributes` to set the `DelaySeconds` attribute of a queue named `MyQueue` to 45 seconds\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue/
?Action=SetQueueAttributes
&DelaySeconds=45
&Expires=2015-12-20T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```