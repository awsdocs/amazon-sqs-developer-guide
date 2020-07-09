# Amazon SQS dead\-letter queues<a name="sqs-dead-letter-queues"></a>

Amazon SQS supports *dead\-letter queues*, which other queues \(*source queues*\) can target for messages that can't be processed \(consumed\) successfully\. Dead\-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed\. For information about creating a queue and configuring a dead\-letter queue for it using the Amazon SQS console, see [Configuring a dead\-letter queue \(console\)](sqs-configure-dead-letter-queue.md)\.

**Important**  
Amazon SQS does *not* create the dead\-letter queue automatically\. You must first create the queue before using it as a dead\-letter queue\.

**Topics**
+ [How do dead\-letter queues work?](#sqs-dead-letter-queues-how-they-work)
+ [What are the benefits of dead\-letter queues?](#sqs-dead-letter-queues-benefits)
+ [How do different queue types handle message failure?](#sqs-dead-letter-queues-handling-message-failure)
+ [When should I use a dead\-letter queue?](#sqs-dead-letter-queues-when-to-use)
+ [Troubleshooting dead\-letter queues](#sqs-dead-letter-queues-troubleshooting)

## How do dead\-letter queues work?<a name="sqs-dead-letter-queues-how-they-work"></a>

Sometimes, messages can’t be processed because of a variety of possible issues, such as erroneous conditions within the producer or consumer application or an unexpected state change that causes an issue with your application code\. For example, if a user places a web order with a particular product ID, but the product ID is deleted, the web store's code fails and displays an error, and the message with the order request is sent to a dead\-letter queue\.

Occasionally, producers and consumers might fail to interpret aspects of the protocol that they use to communicate, causing message corruption or loss\. Also, the consumer’s hardware errors might corrupt message payload\. 

The *redrive policy* specifies the *source queue*, the *dead\-letter queue*, and the conditions under which Amazon SQS moves messages from the former to the latter if the consumer of the source queue fails to process a message a specified number of times\. When the `ReceiveCount` for a message exceeds the `maxReceiveCount` for a queue, Amazon SQS moves the message to a dead\-letter queue \(with its original message ID\)\. For example, if the source queue has a redrive policy with `maxReceiveCount` set to 5, and the consumer of the source queue receives a message 6 times without ever deleting it, Amazon SQS moves the message to the dead\-letter queue\.

To specify a dead\-letter queue, you can use the console or the AWS SDK for Java\. You must do this for each queue that sends messages to a dead\-letter queue\. Multiple queues of the same type can target a single dead\-letter queue\. For more information, see [Configuring a dead\-letter queue \(console\)](sqs-configure-dead-letter-queue.md) and the `RedrivePolicy` attribute of the [ `CreateQueue`](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html#API_CreateQueue_RequestParameters) or [ `SetQueueAttributes`](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html#API_SetQueueAttributes_RequestParameters) action\.

**Important**  
The dead\-letter queue of a FIFO queue must also be a FIFO queue\. Similarly, the dead\-letter queue of a standard queue must also be a standard queue\.  
You must use the same AWS account to create the dead\-letter queue and the other queues that send messages to the dead\-letter queue\. Also, dead\-letter queues must reside in the same region as the other queues that use the dead\-letter queue\. For example, if you create a queue in the US East \(Ohio\) region and you want to use a dead\-letter queue with that queue, the second queue must also be in the US East \(Ohio\) region\.  
The expiration of a message is always based on its original enqueue timestamp\. When a message is moved to a dead\-letter queue, the enqueue timestamp remains unchanged\. For example, if a message spends 1 day in the original queue before being moved to a dead\-letter queue, and the retention period of the dead\-letter queue is set to 4 days, the message is deleted from the dead\-letter queue after 3 days\. Thus, it is a best practice to always set the retention period of a dead\-letter queue to be longer than the retention period of the original queue\.

## What are the benefits of dead\-letter queues?<a name="sqs-dead-letter-queues-benefits"></a>

The main task of a dead\-letter queue is handling message failure\. A dead\-letter queue lets you set aside and isolate messages that can’t be processed correctly to determine why their processing didn’t succeed\. Setting up a dead\-letter queue allows you to do the following:
+ Configure an alarm for any messages delivered to a dead\-letter queue\.
+ Examine logs for exceptions that might have caused messages to be delivered to a dead\-letter queue\.
+ Analyze the contents of messages delivered to a dead\-letter queue to diagnose software or the producer’s or consumer’s hardware issues\.
+ Determine whether you have given your consumer sufficient time to process messages\.

## How do different queue types handle message failure?<a name="sqs-dead-letter-queues-handling-message-failure"></a>

### Standard queues<a name="dead-letter-queues-standard-queues"></a>

[Standard queues](standard-queues.md) keep processing messages until the expiration of the retention period\. This ensures continuous processing of messages, which minimizes the chances of your queue being blocked by messages that can’t be processed\. It also ensures fast recovery for your queue\.

In a system that processes thousands of messages, having a large number of messages that the consumer repeatedly fails to acknowledge and delete might increase costs and place extra load on the hardware\. Instead of trying to process failing messages until they expire, it is better to move them to a dead\-letter queue after a few processing attempts\.

**Note**  
Standard queues allow a high number of inflight messages\. If the majority of your messages can’t be consumed and aren’t sent to a dead\-letter queue, your rate of processing valid messages can slow down\. Thus, to maintain the efficiency of your queue, you must ensure that your application handles message processing correctly\.

### FIFO queues<a name="dead-letter-queues-FIFO-queues"></a>

[FIFO queues](FIFO-queues.md) ensure exactly\-once processing by consuming messages in sequence from a message group\. Thus, although the consumer can continue to retrieve ordered messages from another message group, the first message group remains unavailable until the message blocking the queue is processed successfully\.

**Note**  
FIFO queues allow a lower number of inflight messages\. Thus, to ensure that your FIFO queue doesn’t get blocked by a message, you must ensure that your application handles message processing correctly\.

## When should I use a dead\-letter queue?<a name="sqs-dead-letter-queues-when-to-use"></a>

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/checkmark.png)Do use dead\-letter queues with standard queues\. You should always take advantage of dead\-letter queues when your applications don’t depend on ordering\. Dead\-letter queues can help you troubleshoot incorrect message transmission operations\.

**Note**  
Even when you use dead\-letter queues, you should continue to monitor your queues and retry sending messages that fail for transient reasons\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/checkmark.png)Do use dead\-letter queues to decrease the number of messages and to reduce the possibility of exposing your system to *poison\-pill messages* \(messages that can be received but can’t be processed\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/cross.png)Don’t use a dead\-letter queue with standard queues when you want to be able to keep retrying the transmission of a message indefinitely\. For example, don’t use a dead\-letter queue if your program must wait for a dependent process to become active or available\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/cross.png)Don’t use a dead\-letter queue with a FIFO queue if you don’t want to break the exact order of messages or operations\. For example, don’t use a dead\-letter queue with instructions in an Edit Decision List \(EDL\) for a video editing suite, where changing the order of edits changes the context of subsequent edits\.

## Troubleshooting dead\-letter queues<a name="sqs-dead-letter-queues-troubleshooting"></a>

In some cases, Amazon SQS dead\-letter queues might not always behave as expected\. This section gives an overview of common issues and shows how to resolve them\.

### Viewing messages using the console might cause messages to be moved to a dead\-letter queue<a name="sqs-dlq-console"></a>

Amazon SQS counts viewing a message in the console against the corresponding queue's redrive policy\. Thus, if you view a message in the console the number of times specified in the corresponding queue's redrive policy, the message is moved to the corresponding queue's dead\-letter queue\.

To adjust this behavior, you can do one of the following:
+ Increase the **Maximum Receives** setting for the corresponding queue's redrive policy\.
+ Avoid viewing the corresponding queue's messages in the console\.

### The `NumberOfMessagesSent` and `NumberOfMessagesReceived` for a dead\-letter queue don't match<a name="sqs-dlq-number-of-messages"></a>

If you send a message to a dead\-letter queue manually, it is captured by the `NumberOfMessagesSent` metric\. However, if a message is sent to a dead\-letter queue as a result of a failed processing attempt, it isn't captured by this metric\. Thus, it is possible for the values of `NumberOfMessagesSent` and `NumberOfMessagesReceived` to be different\.