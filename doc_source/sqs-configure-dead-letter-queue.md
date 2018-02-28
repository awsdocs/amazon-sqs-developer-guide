# Tutorial: Configuring an Amazon SQS Dead\-Letter Queue<a name="sqs-configure-dead-letter-queue"></a>

A dead\-letter queue is a queue that other \(source\) queues can target for messages that can't be processed \(consumed\) successfully\. The following example demonstrates how to create a queue and to configure a dead\-letter queue for it\. For more information, see [Using Amazon SQS Dead\-Letter Queues](sqs-dead-letter-queues.md)\.

**Important**  
The dead\-letter queue of a FIFO queue must also be a FIFO queue\. Similarly, the dead\-letter queue of a standard queue must also be a standard queue\.

## AWS Management Console<a name="configure-dead-letter-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-name.png)

1. **Standard** is selected by default\. Choose **FIFO**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-type-descriptions.png)

1. Choose **Configure Queue**\.

1. In this example, you enable the redrive policy for your new queue, set the `MyDeadLetterQueue.fifo` queue as the dead\-letter queue, and set the number of maximum receives to `50`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-dead-letter-queue-configure-parameters.png)

   1. To configure the dead\-letter queue, choose **Use Redrive Policy**\.

   1. Enter the name of the existing **Dead Letter Queue** to which you want sources queues to send messages\.

   1. To configure the number of times that a message can be received before being sent to a dead\-letter queue, set **Maximum Receives** to a value between 1 and 1,000\.
**Note**  
The **Maximum Receives** setting applies only to individual messages\. 

   1. Choose **Create Queue**\.

   Your new queue is configured to use a dead\-letter queue, created, and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

   Your queue's **Maximum Receives** and **Dead Letter Queue** ARN are displayed on the **Redrive Policy** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-dead-letter-queue-redrive-policy.png)

## Java<a name="configure-dead-letter-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To configure a dead\-letter queue<a name="configure-dead-letter-queue-java-api"></a>

1. Copy the example program for a [standard queue](standard-queues.md#standard-queues-getting-started-java) or a [FIFO queue](FIFO-queues.md#FIFO-queues-getting-started-java)\.

1. Set a string that contains JSON\-formatted parameters and values for the `RedrivePolicy` queue attribute:

   ```
   final String redrivePolicy = "{\"maxReceiveCount\":\"5\", \"deadLetterTargetArn\":\"arn:aws:sqs:us-east-2:123456789012:MyDeadLetterQueue\"}";
   ```

1. Use the `CreateQueue` or `SetQueueAttributesRequest` API action to set the `RedrivePolicy` queue attribute:

   ```
   final SetQueueAttributesRequest queueAttributes = new SetQueueAttributesRequest();
   final Map<String,String> attributes = new HashMap<String,String>();            
   attributes.put("RedrivePolicy", redrivePolicy);            
   queueAttributes.setAttributes(attributes);
   queueAttributes.setQueueUrl(myQueueUrl);
   sqs.setQueueAttributes(queueAttributes);
   ```

1. Compile and run your program\.

   The dead\-letter queue is configured\.

#### Sample Request<a name="dead-letter-queue-sample-request"></a>

```
https://sqs.us-east-2.amazonaws.com/123456789012/MySourceQueue 
?Action=SetQueueAttributes
&Attribute.1.Value=%7B%22maxReceiveCount%22%3A%225%22%2C+%22deadLetterTargetArn%22%3A%22arn%3Aaws%3Asqs%3Aus-east-2%3A123456789012%3AMyDeadLetterQueue%22%7D
&Attribute.1.Name=RedrivePolicy
&Version=2012-11-05
```

**Note**  
Queue names and queue URLs are case\-sensitive\.

#### Sample Response<a name="dead-letter-queue-sample-response"></a>

```
<SetQueueAttributesResponse xmlns="https://queue.amazonaws.com/doc/2012-11-05/">
   <ResponseMetadata>
      <RequestId>40945605-b328-53b5-aed4-1cc24a7240e8</RequestId>
   </ResponseMetadata>
</SetQueueAttributesResponse>
```