# What Is Amazon Simple Queue Service?<a name="welcome"></a>

Amazon Simple Queue Service \(Amazon SQS\) offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components\. Amazon SQS offers common constructs such as [dead\-letter queues](sqs-dead-letter-queues.md) and [cost allocation tags](sqs-queue-tags.md)\. It provides a generic web services API and it can be accessed by any programming language that the AWS SDK supports\.

Amazon SQS supports both [standard](standard-queues.md) and [FIFO queues](FIFO-queues.md)\. For more information, see [What Type of Queue Do I Need?](#sqs-queue-types)

**Topics**
+ [What Are the Main Benefits of Amazon SQS?](#sqs-benefits)
+ [How is Amazon SQS Different from Amazon MQ or Amazon SNS?](#sqs-difference-from-amazon-mq-sns)
+ [What Type of Queue Do I Need?](#sqs-queue-types)
+ [How Can I Get Started with Amazon SQS?](#get-started)
+ [We Want to Hear from You](#sqs-we-want-to-hear-from-you)

## What Are the Main Benefits of Amazon SQS?<a name="sqs-benefits"></a>
+ **Security** – [You control](sqs-authentication-and-access-control.md) who can send messages to and receive messages from an Amazon SQS queue\.

  [Server\-side encryption \(SSE\)](sqs-server-side-encryption.md) lets you transmit sensitive data by protecting the contents of messages in queues using keys managed in AWS Key Management Service \(AWS KMS\)\.
+ **Durability** – To ensure the safety of your messages, Amazon SQS stores them on multiple servers\. Standard queues support [at\-least\-once message delivery](standard-queues.md#standard-queues-at-least-once-delivery), and FIFO queues support [exactly\-once message processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.
+ **Availability** – Amazon SQS uses [redundant infrastructure](sqs-basic-architecture.md) to provide highly\-concurrent access to messages and high availability for producing and consuming messages\. 
+ **Scalability** – Amazon SQS can process each [buffered request](sqs-client-side-buffering-request-batching.md) independently, scaling transparently to handle any load increases or spikes without any provisioning instructions\.
+ **Reliability** – Amazon SQS locks your messages during processing, so that multiple producers can send and multiple consumers can receive messages at the same time\. 
+ **Customization** – Your queues don't have to be exactly alike—for example, you can [set a default delay on a queue](sqs-delay-queues.md)\. You can store the contents of messages larger than 256 KB [using Amazon Simple Storage Service \(Amazon S3\)](sqs-s3-messages.md) or Amazon DynamoDB, with Amazon SQS holding a pointer to the Amazon S3 object, or you can split a large message into smaller messages\.

## How Is Amazon SQS Different from Amazon MQ or Amazon SNS?<a name="sqs-difference-from-amazon-mq-sns"></a>

Amazon SQS and [Amazon SNS](https://aws.amazon.com/sns/) are queue and topic services that are highly scalable, simple to use, and don't require you to set up message brokers\. We recommend these services for new applications that can benefit from nearly unlimited scalability and simple APIs\.

[Amazon MQ](https://aws.amazon.com/amazon-mq/) is a managed message broker service that provides compatibility with many popular message brokers\. We recommend Amazon MQ for migrating applications from existing message brokers that rely on compatibility with APIs such as JMS or protocols such as AMQP, MQTT, OpenWire, and STOMP\.

## What Type of Queue Do I Need?<a name="sqs-queue-types"></a>


| Standard Queue | FIFO Queue | 
| --- | --- | 
|  Available in all regions\. **Unlimited Throughput** – Standard queues support a nearly unlimited number of transactions per second \(TPS\) per action\. **At\-Least\-Once Delivery** – A message is delivered at least once, but occasionally more than one copy of a message is delivered\. **Best\-Effort Ordering** – Occasionally, messages might be delivered in an order different from which they were sent\.  | Available in the US East \(Ohio\), US East \(N\. Virginia\), US West \(N\. California\), US West \(Oregon\), Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), Canada \(Central\), EU \(Frankfurt\), China \(Ningxia\), EU \(Ireland\), EU \(London\), EU \(Paris\), EU \(Stockholm\), and South America \(São Paulo\) Regions\. High Throughput – By default, FIFO queues support up to 3,000 messages per second with [batching](sqs-batch-api-actions.md)\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-sqs)\. FIFO queues support up to 300 messages per second, per action \(SendMessage, ReceiveMessage, or DeleteMessage\) without batching\.**Exactly\-Once Processing** – A message is delivered once and remains available until a consumer processes and deletes it\. Duplicates aren't introduced into the queue\.**First\-In\-First\-Out Delivery** – The order in which messages are sent and received is strictly preserved\. | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-standard-queue-diagram.png)  |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-fifo-queue-diagram.png)  | 
|  Send data between applications when the throughput is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  |  Send data between applications when the order of events is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  | 

## How Can I Get Started with Amazon SQS?<a name="get-started"></a>
+ To create your first queue with Amazon SQS and send, receive, and delete a message, see [Getting Started with Amazon SQS](sqs-getting-started.md)\.
+ To discover the functionality and architecture of Amazon SQS, see [How Amazon SQS Works](sqs-how-it-works.md)\.
+ To find out the guidelines and caveats that will help you make the most of Amazon SQS, see [Best Practices for Amazon SQS](sqs-best-practices.md)\.
+ To learn about Amazon SQS actions, see the *Amazon Simple Queue Service API Reference*\.
+ To learn about Amazon SQS AWS CLI commands, see [Amazon SQS in the *AWS CLI Command Reference*](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html)\.

## We Want to Hear from You<a name="sqs-we-want-to-hear-from-you"></a>

We welcome your feedback\. To contact us, visit the [Amazon SQS Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.