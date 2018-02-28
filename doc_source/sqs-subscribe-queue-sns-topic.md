# Tutorial: Subscribing an Amazon SQS Queue to an Amazon SNS Topic<a name="sqs-subscribe-queue-sns-topic"></a>

You can subscribe one or more Amazon SQS queues to an Amazon SNS topic from a list of topics available for the selected queue\. Amazon SQS manages the subscription and any necessary permissions\. When you publish a message to a topic, Amazon SNS sends the message to every subscribed queue\. For more information about Amazon SNS, see [What is Amazon Simple Notification Service?](http://docs.aws.amazon.com/sns/latest/dg/welcome.html) in the *Amazon Simple Notification Service Developer Guide*\.

The following example demonstrates subscribing an existing Amazon SQS queue to an existing Amazon SNS topic\.

**Note**  
Amazon SNS isn't currently compatible with FIFO queues\.  
For information about using Amazon SNS with encrypted Amazon SQS queues, see [Example 3: Enable Compatibility Between AWS Services Such as Amazon CloudWatch Events, Amazon S3, and Amazon SNS and Encrypted Queues](sqs-server-side-encryption.md#compatibility-with-aws-services)\.  
When you subscribe an Amazon SQS queue to an Amazon SNS topic, Amazon SNS uses HTTPS to forward messages to Amazon SQS\.

## AWS Management Console<a name="subscribe-queue-to-sns-topic-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the list of queues, choose the queue \(or queues\) to which you want to subscribe an Amazon SNS topic\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-subscribe-queue-to-sns-topic-choose-queue.png)

1. From **Queue Actions**, select **Subscribe Queue to SNS Topic** \(or **Subscribe Queues to SNS Topic**\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-subscribe-queue-to-sns-topic-drop-down.png)

   The **Subscribe to a Topic** dialog box is displayed\.

1. From the **Choose a Topic** drop\-down list, select an Amazon SNS topic to which you want to subscribe your queue \(or queues\), select the **Topic Region** \(optional\), and then choose **Subscribe**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-subscribe-queue-to-sns-topic-dialog-box.png)
**Note**  
Typing a different **Topic ARN** is useful when you want to subscribe a queue to an Amazon SNS topic from an AWS account other than the one you used to create your Amazon SQS queue\.  
This is also useful if the Amazon SNS topic isn't listed in the **Choose a Topic** drop\-down list\.

   The **Topic Subscription Result** dialog box is displayed\.

1. Review the list of Amazon SQS queues that are subscribed to the Amazon SNS topic and choose **OK**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-subscribe-queue-to-sns-topic-subscription-result.png)

   The queue is subscribed to the topic\.
**Note**  
If your Amazon SQS queue and Amazon SNS topic are in different AWS accounts, the owner of the topic must first confirm the subscription\. For more information, see [Confirm the Subscription](http://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.html#SendMessageToHttp.confirm) in the *Amazon Simple Notification Service Developer Guide*\.  
To list your subscriptions, unsubscribe from topics, and delete topics, use the Amazon SNS console\. For more information, see [Clean Up](http://docs.aws.amazon.com/sns/latest/dg/CleanUp.html)\.

   To verify the results of the subscription, you can publish to the topic and then view the message that the topic sends to the queue\. For more information, see [Sending Amazon SNS Messages to Amazon SQS Queues](http://docs.aws.amazon.com/sns/latest/dg/SendMessageToSQS.html) in the *Amazon Simple Notification Service Developer Guide*\.