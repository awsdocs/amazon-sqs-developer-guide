# Tutorial: Creating an Amazon SQS Queue<a name="sqs-create-queue"></a>

The first and most common Amazon SQS task is creating queues\. The following example demonstrates how to create and configure a queue\.


+ [AWS Management Console](#create-queue-console)
+ [Java](#create-queue-java)
+ [AWS CloudFormation](#create-queue-cloudformation)

## AWS Management Console<a name="create-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-name.png)

1. **Standard** is selected by default\. Choose **FIFO**\.

1. Create your queue\.

   + To create your queue with the default parameters, choose **Quick\-Create Queue**\.

   + To configure your queue's parameters, choose **Configure Queue**\. When you finish configuring the parameters, choose **Create Queue**\. For more information about creating a queue with SSE, see [Tutorial: Creating an Amazon SQS Queue with Server\-Side Encryption](sqs-create-queue-sse.md)\.

     The following example shows the **Content\-Based Deduplication** parameter specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-configure-parameters.png)

   Your new queue is created and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

   The **Queue Type** column helps you distinguish standard queues from FIFO queues at a glance\. For a FIFO queue, the **Content\-Based Deduplication** column displays whether you have enabled [exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-queue-type-content-based-deduplication-columns.png)

   Your queue's **Name**, **URL**, and **ARN** are displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-details-url-arn.png)

## Java<a name="create-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To create a standard queue<a name="create-queue-java-standard"></a>

1. Copy the [example program](standard-queues.md#standard-queues-getting-started-java)\.

   The following section of the code creates the `MyQueue` queue:

   ```
   // Create a queue
   System.out.println("Creating a new SQS queue called MyQueue.\n");
   final CreateQueueRequest createQueueRequest = new CreateQueueRequest("MyQueue");
   final String myQueueUrl = sqs.createQueue(createQueueRequest).getQueueUrl();
   ```

1. Compile and run the example\.

   The queue is created\.

### To create a FIFO queue<a name="create-queue-java-FIFO"></a>

1. Copy the [example program](FIFO-queues.md#FIFO-queues-getting-started-java)\.

   The following section of the code creates the `MyFifoQueue.fifo` queue:

   ```
   // Create a FIFO queue
   System.out.println("Creating a new Amazon SQS FIFO queue called " + "MyFifoQueue.fifo.\n");
   final Map<String, String> attributes = new HashMap<String, String>();
   
   // A FIFO queue must have the FifoQueue attribute set to True
   attributes.put("FifoQueue", "true");
   
   // If the user doesn't provide a MessageDeduplicationId, generate a MessageDeduplicationId based on the content.
   attributes.put("ContentBasedDeduplication", "true");
   
   // The FIFO queue name must end with the .fifo suffix
   final CreateQueueRequest createQueueRequest = new CreateQueueRequest("MyFifoQueue.fifo").withAttributes(attributes);
   final String myQueueUrl = sqs.createQueue(createQueueRequest).getQueueUrl();
   ```

1. Compile and run the example\.

   The queue is created\.

## AWS CloudFormation<a name="create-queue-cloudformation"></a>

You can use the AWS CloudFormation console and a JSON \(or YAML\) template to create an Amazon SQS queue\. For more information, see [Working with AWS CloudFormation Templates](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and the [`AWS::SQS::Queue` Resource](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-sqs-queues.html) in the *AWS CloudFormation User Guide*\.

1. Copy the following JSON code to a file named `MyQueue.json`\. To create a standard queue, omit the `FifoQueue` and `ContentBasedDeduplication` properties\. For more information on content\-based deduplication, see [Exactly\-Once Processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.

   ```
   {
      "AWSTemplateFormatVersion": "2010-09-09",
      "Resources": {
         "MyQueue": {
            "Properties": {
               "QueueName": "MyQueue.fifo",
               "FifoQueue": true,
               "ContentBasedDeduplication": true
                },
            "Type": "AWS::SQS::Queue"
            }
         },
      "Outputs": {
         "QueueName": {
            "Description": "The name of the queue",
            "Value": {
               "Fn::GetAtt": [
                  "MyQueue",
                  "QueueName"
               ]
            }
         },
         "QueueURL": {
            "Description": "The URL of the queue",
            "Value": {
               "Ref": "MyQueue"
            }
         },
         "QueueARN": {
            "Description": "The ARN of the queue",
            "Value": {
               "Fn::GetAtt": [
                  "MyQueue",
                  "Arn"
               ]
            }
         }
      }
   }
   ```

1. Sign in to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation), and then choose **Create Stack**\.

1. On the **Select Template** page, choose **Upload a template to Amazon S3**, choose your `MyQueue.json` file, and then choose **Next**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-select-template.png)

1. On the **Specify Details** page, type `MyQueue` for **Stack Name**, and then choose **Next**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-stack-name.png)

1. On the **Options** page, choose **Next**\.

1. On the **Review** page, choose **Create**\.

   AWS CloudFormation begins to create the `MyQueue` stack and displays the **CREATE\_IN\_PROGRESS** status\. When the process is complete, AWS CloudFormation displays the **CREATE\_COMPLETE** status\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-create-complete.png)

1. \(Optional\) To display the name, URL, and ARN of the queue, choose the name of the stack and then on the next page expand the **Outputs** section\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-name-URL-ARN.png)