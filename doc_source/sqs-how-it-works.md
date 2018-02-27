# How Amazon SQS Queues Work<a name="sqs-how-it-works"></a>

This section describes the types of Amazon SQS queues and their basic properties\. It also describes the identifiers of queues and messages, and various queue and message management workflows\. 


+ [Basic Prerequisites](#sqs-basic-requirements)
+ [Standard Queues](standard-queues.md)
+ [FIFO \(First\-In\-First\-Out\) Queues](FIFO-queues.md)
+ [Queue and Message Identifiers](sqs-queue-message-identifiers.md)
+ [Resources Required to Process Messages](sqs-resources-required-process-messages.md)
+ [Visibility Timeout](sqs-visibility-timeout.md)
+ [Using Amazon SQS Dead\-Letter Queues](sqs-dead-letter-queues.md)
+ [Message Lifecycle](sqs-message-lifecycle.md)
+ [Tagging Your Amazon SQS Queues](sqs-queue-tags.md)
+ [Using Amazon SQS Message Attributes](sqs-message-attributes.md)
+ [Amazon SQS Long Polling](sqs-long-polling.md)
+ [Amazon SQS Delay Queues](sqs-delay-queues.md)
+ [Amazon SQS Message Timers](sqs-message-timers.md)
+ [Managing Large Amazon SQS Messages Using Amazon S3](sqs-s3-messages.md)
+ [Using JMS with Amazon SQS](sqs-java-message-service-jms-client.md)

## Basic Prerequisites<a name="sqs-basic-requirements"></a>

The following basic prerequisites help you get started with Amazon SQS queues:

+ You must assign a name to each of your queues\. You can get a list of all your queues or a subset of your queues that share the same initial characters in their names\. For example, you can get a list of all your queues whose names start with `T3`\.

+ A queue can be empty if you haven't sent any messages to it or if you have deleted all the messages from it\.

+ You can delete a queue at any time, whether it's empty or not\. By default, a queue retains messages for four days\. However, you can configure a queue to retain messages for up to 14 days after the message is sent\.
**Note**  
Unless your application specifically requires repeatedly creating queues and leaving them inactive or storing large amounts of data in your queue, consider using Amazon S3 for storing your data\.

The following table lists the API actions you can use to work with queues\.


| To do this\.\.\. | Use this action | 
| --- | --- | 
|  Create a queue   |   [CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)   | 
|  Get the URL of an existing queue   |   [GetQueueUrl](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueUrl.html)   | 
|  List your queues   |   [ListQueues](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueues.html)   | 
|  Delete a queue   |   [DeleteQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html)   | 