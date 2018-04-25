# Managing Large Amazon SQS Messages Using Amazon S3<a name="sqs-s3-messages"></a>

You can use Amazon S3 and the Amazon SQS Extended Client Library for Java to manage Amazon SQS messages\. This is especially useful for storing and consuming messages up to 2 GB in size\. Unless your application requires repeatedly creating queues and leaving them inactive or storing large amounts of data in your queue, consider using Amazon S3 for storing your data\.

You can use the Amazon SQS Extended Client Library for Java library to do the following:

+ Specify whether messages are always stored in Amazon S3 or only when the size of a message exceeds 256 KB\.

+ Send a message that references a single message object stored in an Amazon S3 bucket\. 

+ Get the corresponding message object from an Amazon S3 bucket\.

+ Delete the corresponding message object from an Amazon S3 bucket\.

**Note**  
The [SDK for Java](https://aws.amazon.com/sdkforjava/) and Amazon SQS Extended Client Library for Java require the J2SE Development Kit 8\.0 or later\.  
You can use the Amazon SQS Extended Client Library for Java to manage Amazon SQS messages using Amazon S3\. However, you can't do this using the AWS CLI, the Amazon SQS console, the Amazon SQS HTTP API, or any of the AWS SDKs—except for the SDK for Java\.