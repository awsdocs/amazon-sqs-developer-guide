# Amazon SQS Long Polling<a name="sqs-long-polling"></a>

*Long polling* helps reduce your cost of using Amazon SQS by reducing the number of empty responses \(when there are no messages available to return in reply to a `ReceiveMessage` request sent to an Amazon SQS queue\) and eliminating false empty responses \(when messages are available in the queue but aren't included in the response\):

+ Long polling reduces the number of empty responses by allowing Amazon SQS to wait until a message is available in the queue before sending a response\. Unless the connection times out, the response to the `ReceiveMessage` request contains at least one of the available messages, up to the maximum number of messages specified in the `ReceiveMessage` action\.

+ Long polling eliminates false empty responses by querying all \(rather than a limited number\) of the servers\.

+ Long polling returns messages as soon any message becomes available\.


+ [The Differences Between Short and Long Polling](#sqs-short-long-polling-differences)
+ [Enabling Long Polling using the AWS Management Console](#sqs-long-polling-console)
+ [Enabling Long Polling Using the API](#sqs-long-polling-api)
+ [Enabling Long Polling Using the Query API](#sqs-long-polling-query-api)

## The Differences Between Short and Long Polling<a name="sqs-short-long-polling-differences"></a>

Amazon SQS uses *short polling* by default, querying only a subset of the servers \(based on a weighted random distribution\) to determine whether any messages are available for inclusion in the response\.

Short polling occurs when the `WaitTimeSeconds` parameter of a `ReceiveMessage` call is set to `0` in one of two ways:

+ The `ReceiveMessage` call sets `WaitTimeSeconds` to `0`\.

+ The `ReceiveMessage` call doesn’t set `WaitTimeSeconds` and the queue attribute `ReceiveMessageWaitTimeSeconds` is set to `0`\.

**Note**  
For the `WaitTimeSeconds` parameter of `ReceiveMessage`, a value set between `1` and `20` has priority over any value set for the queue attribute `ReceiveMessageWaitTimeSeconds`\.

## Enabling Long Polling using the AWS Management Console<a name="sqs-long-polling-console"></a>

You can enable long polling using the AWS Management Console by setting a **Receive Message Wait Time** to a value greater than `0`\.

**To enable long polling with the AWS Management Console for a new queue**

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Select **Create New Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. In the **Create New Queue** dialog box, type the **Queue Name**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue_Dialog.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. For **Receive Message Wait Time**, type a positive integer value, from `1` to `20` seconds\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Create_New_Queue_Dialog_Receive.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. Choose **Create Queue**\.

You can use the AWS Management Console to change the **Receive Message Wait Time** setting for an existing queue\.

**To set a new Receive Message Wait Time value for an existing queue**

1. Select a queue\.

1. From the **Queue Actions** drop\-down list, select **Configure Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-queue.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. For **Receive Message Wait Time**, type a positive integer value\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AWS_Console_Queue_Actions_Configure_Queue_Receive.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. Choose **Save Changes**\.

## Enabling Long Polling Using the API<a name="sqs-long-polling-api"></a>

The following table lists the API actions to use\.


| Use this action | Use\.\.\. | 
| --- | --- | 
| [ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) |  `WaitTimeSeconds` parameter  | 
| `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` |  `ReceiveMessageWaitTimeSeconds` attribute  | 
|  `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`  |  `ReceiveMessageWaitTimeSeconds` attribute  | 

**Important**  
If you decide to implement long polling with multiple queues, we recommend using one thread for each queue instead of trying to use a single thread for polling all of the queues\.  
When you use one thread for each queue, your application can process the messages in each of the queues as they become available\. A single thread for multiple queues might cause your application to become blocked from processing available messages in the other queues while waiting \(up to 20 seconds\) for a queue that doesn't have any available messages\.

In most cases, when using long polling, set the timeout value to a maximum of 20 seconds\. If the 20\-second maximum doesn't work for your application, set a shorter timeout for long polling \(the minimum is 1 second\)\. If you don't use an AWS SDK to access Amazon SQS, or if you configure an AWS SDK to have a shorter timeout, you may need to modify your Amazon SQS client to allow for longer requests or to use a shorter timeout for long polling\.

## Enabling Long Polling Using the Query API<a name="sqs-long-polling-query-api"></a>

The following example enables long polling by calling the `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` action with the `WaitTimeSeconds` parameter set to 10 seconds\.

The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue/
?Action=ReceiveMessage
&WaitTimeSeconds=10
&MaxNumberOfMessages=5
&VisibilityTimeout=15
&AttributeName=All;
&Expires=2013-10-25T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```

The following example shows another way to enable long polling\. Here, the `ReceiveMessageWaitTimeSeconds` attribute for the `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action is set to 20 seconds\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue/
?Action=SetQueueAttributes
&Attribute.Name=ReceiveMessageWaitTimeSeconds
&Attribute.Value=20
&Expires=2013-10-25T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```