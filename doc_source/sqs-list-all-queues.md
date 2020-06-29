# Tutorial: Listing all Amazon SQS queues in a Region<a name="sqs-list-all-queues"></a>

When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\. In this tutorial you learn how to confirm your queue's existence by listing all queues in the current region\.

**Topics**
+ [AWS Management Console](#list-all-queues-console)
+ [AWS SDK for Java](#list-all-queues-java)

## AWS Management Console<a name="list-all-queues-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Your queues in the current region are listed\.

   The **Queue Type** column helps you distinguish standard queues from FIFO queues at a glance\. For a FIFO queue, the **Content\-Based Deduplication** column displays whether you have enabled [exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-type-content-based-deduplication-columns.png)

   Your queue's **Name**, **URL**, and **ARN** are displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-details-url-arn.png)

## AWS SDK for Java<a name="list-all-queues-java"></a>

 The following example uses the AWS Java SDK\. To install and set up the SDK, see [Set up the AWS SDK for Java](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-install.html) in the *AWS SDK for Java Developer Guide*\.

Before you run the example code, configure your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\. 

 The `listQueues` and `listDeadLetterSourceQueues` API methods now support pagination\. Set parameter `MaxResults` in the `listQueues` or `listDeadLetterSourceQueues` request to specify the maximum number of results to be returned in the response\. If you do not set `MaxResults`, the response includes a maximum of 1,000 results\. If there are additional results to display, the response includes a value for `NextToken`\. Use `NextToken` as a parameter in your next request to `listQueues` to receive the next page of results\. If the `NextToken` value in the response is null, there are no additional results to request\. 

**To list all queues**

1. Copy the [standard queue example program](standard-queues-getting-started-java.md) or the [FIFO queue example program](FIFO-queues-getting-started-java.md)\.

   The following section of the code list all \(or the first 1000\) queues in the current region:

   ```
   // List queues
   System.out.println("List all (or the first 1000) queues in the current region.\n");
   for (final String queueUrl : sqs.listQueues().getQueueUrls()) {
       System.out.println("  QueueUrl: " + queueUrl);
   }
   System.out.println();
   ```

1. Compile and run the example\.

   All queues in the current region created using API version 2012\-11\-05 are listed\. The response include the following items:
   + The unique *queue URL*\.
   + The *request ID* that Amazon SQS assigned to your request\.