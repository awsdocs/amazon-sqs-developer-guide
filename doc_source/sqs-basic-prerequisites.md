# Basic Prerequisites<a name="sqs-basic-prerequisites"></a>

The following basic prerequisites help you get started with Amazon SQS queues:

+ You must assign a name to each of your queues\. You can get a list of all your queues or a subset of your queues that share the same initial characters in their names\. For example, you can get a list of all your queues whose names start with `T3`\.

+ A queue can be empty if you haven't sent any messages to it or if you have deleted all the messages from it\.

+ You can delete a queue at any time, whether it's empty or not\. By default, a queue retains messages for four days\. However, you can configure a queue to retain messages for up to 14 days after the message is sent\.
**Note**  
Unless your application specifically requires repeatedly creating queues and leaving them inactive or storing large amounts of data in your queue, consider using Amazon S3 for storing your data\.

The following table lists the API actions you can use to work with queues\.


| Task | API Action | 
| --- | --- | 
| Create a queue\. | [CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html) | 
| Get the URL of an existing queue\. | [GetQueueUrl](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueUrl.html) | 
| List your queues\. | [ListQueues](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueues.html) | 
| Delete a queue\. | [DeleteQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html) | 