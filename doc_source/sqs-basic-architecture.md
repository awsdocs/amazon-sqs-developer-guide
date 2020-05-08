# Basic Amazon SQS architecture<a name="sqs-basic-architecture"></a>

   Learn about the parts of a distributed messaging system and the lifecycle of an Amazon SQS message, from creation to deletion\.   

This section outlines the parts of a distributed messaging system and explains the lifecycle of an Amazon SQS message\.

## Distributed queues<a name="sqs-distributed-queue"></a>

There are three main parts in a distributed messaging system: the components of your distributed system, your queue \(distributed on Amazon SQS servers\), and the messages in the queue\.

In the following scenario, your system has several *producers* \(components that send messages to the queue\) and *consumers* \(components that receive messages from the queue\)\. The queue \(which holds messages A through E\) redundantly stores the messages across multiple Amazon SQS servers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview.png)

## Message lifecycle<a name="sqs-message-lifecycle"></a>

The following scenario describes the lifecycle of an Amazon SQS message in a queue, from creation to deletion\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-message-lifecycle-diagram.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) A producer \(component 1\) sends message A to a queue, and the message is distributed across the Amazon SQS servers redundantly\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) When a consumer \(component 2\) is ready to process messages, it consumes messages from the queue, and message A is returned\. While message A is being processed, it remains in the queue and isn't returned to subsequent receive requests for the duration of the [visibility timeout](sqs-visibility-timeout.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) The consumer \(component 2\) deletes message A from the queue to prevent the message from being received and processed again when the visibility timeout expires\.

**Note**  
Amazon SQS automatically deletes messages that have been in a queue for more than maximum message retention period\. The default message retention period is 4 days\. However, you can set the message retention period to a value from 60 seconds to 1,209,600 seconds \(14 days\) using the `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action\.