# Amazon SQS Message Lifecycle<a name="sqs-message-lifecycle"></a>

The following diagram describes the lifecycle of an Amazon SQS message, from creation to deletion\. In this example, a queue already exists\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-message-lifecycle-diagram.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) Component 1 sends Message A to a queue, and the message is distributed across the Amazon SQS servers redundantly\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) When Component 2 is ready to process a message, it consumes messages from the queue, and Message A is returned\. While Message A is being processed, it remains in the queue and isn't returned to subsequent receive requests for the duration of the visibility timeout\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) Component 2 deletes Message A from the queue to prevent the message from being received and processed again once the visibility timeout expires\.

**Note**  
Amazon SQS automatically deletes messages that have been in a queue for more than maximum message retention period\. The default message retention period is 4 days\. However, you can set the message retention period to a value from 60 seconds to 1,209,600 seconds \(14 days\) using the `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action\.