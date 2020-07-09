# Creating an Amazon SQS queue \(AWS CloudFormation\)<a name="create-queue-cloudformation"></a>

You can use the AWS CloudFormation console and a JSON \(or YAML\) template to create an Amazon SQS queue\. For more information, see [Working with AWS CloudFormation Templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and the [`AWS::SQS::Queue` Resource](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-sqs-queues.html) in the *AWS CloudFormation User Guide*\.

**To use AWS CloudFormation to create an Amazon SQS queue\.**

1. Copy the following JSON code to a file named `MyQueue.json`\. To create a standard queue, omit the `FifoQueue` and `ContentBasedDeduplication` properties\. For more information on content\-based deduplication, see [Exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\.

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

1. On the **Specify Template** panel, choose **Upload a template file**, choose your `MyQueue.json` file, and then choose **Next**\.

1. On the **Specify Details** page, type `MyQueue` for **Stack Name**, and then choose **Next**\.

1. On the **Options** page, choose **Next**\.

1. On the **Review** page, choose **Create**\.

   AWS CloudFormation begins to create the `MyQueue` stack and displays the **CREATE\_IN\_PROGRESS** status\. When the process is complete, AWS CloudFormation displays the **CREATE\_COMPLETE** status\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-create-complete.png)

1. \(Optional\) To display the name, URL, and ARN of the queue, choose the name of the stack and then on the next page expand the **Outputs** section\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-queue-cfn-name-URL-ARN.png)