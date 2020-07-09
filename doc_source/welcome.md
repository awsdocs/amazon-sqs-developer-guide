# What is Amazon Simple Queue Service?<a name="welcome"></a>

Amazon Simple Queue Service \(Amazon SQS\) offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components\. Amazon SQS offers common constructs such as [dead\-letter queues](sqs-dead-letter-queues.md) and [cost allocation tags](sqs-queue-tags.md)\. It provides a generic web services API and it can be accessed by any programming language that the AWS SDK supports\.

Amazon SQS supports both [standard](standard-queues.md) and [FIFO queues](FIFO-queues.md)\. For more information, see [What type of queue do I need?](#sqs-queue-types)

**Topics**
+ [What are the main benefits of Amazon SQS?](#sqs-benefits)
+ [How is Amazon SQS different from Amazon MQ or Amazon SNS?](#sqs-difference-from-amazon-mq-sns)
+ [What type of queue do I need?](#sqs-queue-types)
+ [How can I get started with Amazon SQS?](#get-started)
+ [We want to hear from you](#sqs-we-want-to-hear-from-you)

## What are the main benefits of Amazon SQS?<a name="sqs-benefits"></a>
+ **Security** – [You control](sqs-authentication-and-access-control.md) who can send messages to and receive messages from an Amazon SQS queue\.

  [Server\-side encryption \(SSE\)](sqs-server-side-encryption.md) lets you transmit sensitive data by protecting the contents of messages in queues using keys managed in AWS Key Management Service \(AWS KMS\)\.
+ **Durability** – To ensure the safety of your messages, Amazon SQS stores them on multiple servers\. Standard queues support [at\-least\-once message delivery](standard-queues.md#standard-queues-at-least-once-delivery), and FIFO queues support [exactly\-once message processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.
+ **Availability** – Amazon SQS uses [redundant infrastructure](sqs-basic-architecture.md) to provide highly\-concurrent access to messages and high availability for producing and consuming messages\. 
+ **Scalability** – Amazon SQS can process each [buffered request](sqs-client-side-buffering-request-batching.md) independently, scaling transparently to handle any load increases or spikes without any provisioning instructions\.
+ **Reliability** – Amazon SQS locks your messages during processing, so that multiple producers can send and multiple consumers can receive messages at the same time\. 
+ **Customization** – Your queues don't have to be exactly alike—for example, you can [set a default delay on a queue](sqs-delay-queues.md)\. You can store the contents of messages larger than 256 KB [using Amazon Simple Storage Service \(Amazon S3\)](sqs-s3-messages.md) or Amazon DynamoDB, with Amazon SQS holding a pointer to the Amazon S3 object, or you can split a large message into smaller messages\.

## How is Amazon SQS different from Amazon MQ or Amazon SNS?<a name="sqs-difference-from-amazon-mq-sns"></a>

Amazon SQS and [Amazon SNS](https://aws.amazon.com/sns/) are queue and topic services that are highly scalable, simple to use, and don't require you to set up message brokers\. We recommend these services for new applications that can benefit from nearly unlimited scalability and simple APIs\.

[Amazon MQ](https://aws.amazon.com/amazon-mq/) is a managed message broker service that provides compatibility with many popular message brokers\. We recommend Amazon MQ for migrating applications from existing message brokers that rely on compatibility with APIs such as JMS or protocols such as AMQP, MQTT, OpenWire, and STOMP\.

## What type of queue do I need?<a name="sqs-queue-types"></a>


| Standard queue | FIFO queue | 
| --- | --- | 
|  **Unlimited Throughput** – Standard queues support a nearly unlimited number of API calls per second, per API action \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\)\. **At\-Least\-Once Delivery** – A message is delivered at least once, but occasionally more than one copy of a message is delivered\. **Best\-Effort Ordering** – Occasionally, messages might be delivered in an order different from which they were sent\.  | High Throughput – If you use [batching](sqs-batch-api-actions.md), FIFO queues support up to 3,000 transactions per second, per API method \(SendMessageBatch, ReceiveMessage, or DeleteMessageBatch\)\. The 3000 transactions represent 300 API calls, each with a batch of 10 messages\. To request a quota increase, [submit a support request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-sqs)\. Without batching, FIFO queues support up to 300 API calls per second, per API method \(SendMessage, ReceiveMessage, or DeleteMessage\)\. **Exactly\-Once Processing** – A message is delivered once and remains available until a consumer processes and deletes it\. Duplicates aren't introduced into the queue\.**First\-In\-First\-Out Delivery** – The order in which messages are sent and received is strictly preserved\. | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-standard-queue-diagram.png)  |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-fifo-queue-diagram.png)  | 
|  Send data between applications when the throughput is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  |  Send data between applications when the order of events is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  | 

## How can I get started with Amazon SQS?<a name="get-started"></a>
+ To create your first queue with Amazon SQS and send, receive, and delete a message, see [Getting started with Amazon SQS](sqs-getting-started.md)\.
+ To trigger a Lambda function, see [Configuring a queue to trigger an AWS Lambda function \(console\)](sqs-configure-lambda-function-trigger.md)\.
+ To discover the functionality and architecture of Amazon SQS, see [How Amazon SQS works](sqs-how-it-works.md)\.
+ To find out the guidelines and caveats that will help you make the most of Amazon SQS, see [Best practices for Amazon SQS](sqs-best-practices.md)\.
+ Explore the Amazon SQS examples for one of the AWS SDKs, such as the [AWS SDK for Java 2\.x Developer Guide](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/)\. 
+ To learn about Amazon SQS actions, see the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)\.
+ To learn about Amazon SQS AWS CLI commands, see the [ *AWS CLI Command Reference*](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html)\.

## We want to hear from you<a name="sqs-we-want-to-hear-from-you"></a>

We welcome your feedback\. To contact us, visit the [Amazon SQS Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.