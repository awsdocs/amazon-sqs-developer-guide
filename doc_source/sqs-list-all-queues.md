# Tutorial: Listing All Amazon SQS Queues in a Region<a name="sqs-list-all-queues"></a>

When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\. The following example demonstrates confirming your queue's existence by listing all queues in the current region\.

## AWS Management Console<a name="list-all-queues-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Your queues in the current region are listed\.

   The **Queue Type** column helps you distinguish standard queues from FIFO queues at a glance\. For a FIFO queue, the **Content\-Based Deduplication** column displays whether you have enabled [exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-type-content-based-deduplication-columns.png)

   Your queue's **Name**, **URL**, and **ARN** are displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-details-url-arn.png)

## Java<a name="list-all-queues-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

**Note**  
This action is identical for standard and FIFO queues\.

1. Copy the [standard queue example program](standard-queues.md#standard-queues-getting-started-java) or the [FIFO queue example program](FIFO-queues.md#FIFO-queues-getting-started-java)\.

   The following section of the code list all queues in the current region:

   ```
   // List queues
   System.out.println("Listing all queues in your account.\n");
   for (final String queueUrl : sqs.listQueues().getQueueUrls()) {
       System.out.println("  QueueUrl: " + queueUrl);
   }
   System.out.println();
   ```

1. Compile and run the example\.

   All queues in the current region created using API version 2012\-11\-05 are listed\. The response include the following items:

   + The unique *queue URL*\.

   + The *request ID* that Amazon SQS assigned to your request\.