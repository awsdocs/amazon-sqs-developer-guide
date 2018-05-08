# Tutorial: Purging Messages from an Amazon SQS Queue<a name="sqs-purge-queue"></a>

If you don't want to delete an Amazon SQS queue but need to delete all the messages from it, you can purge the queue\. The following example shows how to purge a queue\.

**Important**  
When you purge a queue, you can't retrieve any messages deleted from it\.  
The message deletion process takes up to 60 seconds\. We recommend waiting for 60 seconds regardless of your queue's size\. 

## AWS Management Console<a name="purge-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Purge Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-purging-queue-purge-queue.png)

   The **Purge Queues** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-purging-queue-purge-queues-dialog-box.png)

1. Choose **Yes, Purge Queue**\.

   All messages are purged from the queue\.

   The **Purge Queues** confirmation dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-purging-queue-purge-queues-confirmation-dialog-box.png)

1. Choose **OK**\.