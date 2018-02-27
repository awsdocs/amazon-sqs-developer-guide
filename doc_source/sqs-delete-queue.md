# Tutorial: Deleting an Amazon SQS Queue<a name="sqs-delete-queue"></a>

If you don't use an Amazon SQS queue \(and don't foresee using it in the near future\), it is a best practice to delete it from Amazon SQS\. The following example demonstrates deleting a queue\.

## AWS Management Console<a name="delete-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. From **Queue Actions**, select **Delete Queue**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-deleting-queue-delete-queue.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

   The **Delete Queues** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-deleting-queue-delete-queue-dialog-box.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. Choose **Yes, Delete Queue**\.

   The queue is deleted\.

## Java<a name="delete-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

**Note**  
This action is identical for standard and FIFO queues\.

1. Copy the standard queue example program or the FIFO queue example program\.

   The following section of the code deletes the queue:

   ```
   // Delete the queue
   System.out.println("Deleting the test queue.\n");
   sqs.deleteQueue(new DeleteQueueRequest(myQueueUrl));
   ```

1. Compile and run the example\.

   The queue is deleted\.