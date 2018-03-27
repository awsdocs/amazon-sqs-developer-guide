# What is Amazon Simple Queue Service?<a name="welcome"></a>

Amazon Simple Queue Service \(Amazon SQS\) offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components\. Amazon SQS offers common middleware constructs such as *dead\-letter queues* and *poison\-pill messages*\. It provides a generic web services API and it can be accessed by any programming language that the AWS SDK supports\.

Amazon SQS supports both [standard](standard-queues.md) and [FIFO queues](FIFO-queues.md)\. For more information, see [What Type of Queue Do I Need?](#sqs-queue-types)


+ [What Are the Main Benefits of Amazon SQS?](#sqs-benefits)
+ [How is Amazon SQS Different from Amazon MQ or Amazon SNS?](#sqs-difference-from-amazon-mq-sns)
+ [What Type of Queue Do I Need?](#sqs-queue-types)
+ [We Want to Hear from You](#sqs-we-want-to-hear-from-you)

## What Are the Main Benefits of Amazon SQS?<a name="sqs-benefits"></a>

+ **Security** – You control who can send messages to and receive messages from an Amazon SQS queue\. [ Server\-side encryption \(SSE\)](sqs-server-side-encryption.md) lets you transmit sensitive data by protecting the contents of messages in queues using keys managed in the AWS Key Management Service \(AWS KMS\)\.

+ **Durability** – To ensure the safety of your messages, Amazon SQS stores them on multiple servers\. Standard queues support [at\-least\-once message delivery](standard-queues.md#standard-queues-at-least-once-delivery), while FIFO queues support [exactly\-once message processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.

+ **Availability** – Amazon SQS uses [redundant infrastructure](sqs-basic-architecture.md) to provide highly\-concurrent access to messages and high availability for producing and consuming messages\. 

+ **Scalability** – Amazon SQS can process each [buffered request](sqs-batch-api-actions.md#sqs-client-side-buffering-request-batching) independently, scaling transparently to handle any load increases or spikes without any provisioning instructions\.

+ **Reliability** – Amazon SQS locks your messages during processing, so that multiple producers can send and multiple consumers can receive messages at the same time\. 

+ **Customization** – Your queues don't have to be exactly alike—for example, you can [set a default delay on a queue](sqs-delay-queues.md)\. You can store the contents of messages larger than 256 KB [using Amazon Simple Storage Service \(Amazon S3\)](sqs-s3-messages.md) or Amazon DynamoDB, with Amazon SQS holding a pointer to the Amazon S3 object, or you can split a large message into smaller messages\.

## How Is Amazon SQS Different from Amazon MQ or Amazon SNS?<a name="sqs-difference-from-amazon-mq-sns"></a>

Amazon SQS and [Amazon SNS](https://aws.amazon.com/sns/) are queue and topic services that are highly scalable, simple to use, and don't require you to set up message brokers\. We recommend these services for new applications that can benefit from nearly unlimited scalability and simple APIs\.

[Amazon MQ](https://aws.amazon.com/amazon-mq/) is a managed message broker service that provides compatibility with many popular message brokers\. We recommend Amazon MQ for migrating applications from existing message brokers that rely on compatibility with APIs such as JMS or protocols such as AMQP, MQTT, OpenWire, and STOMP\.

## What Type of Queue Do I Need?<a name="sqs-queue-types"></a>


| Standard Queue | FIFO Queue | 
| --- | --- | 
|  Available in all regions\. **Unlimited Throughput** – Standard queues support a nearly unlimited number of transactions per second \(TPS\) per API action\. **At\-Least\-Once Delivery** – A message is delivered at least once, but occasionally more than one copy of a message is delivered\. **Best\-Effort Ordering** – Occasionally, messages might be delivered in an order different from which they were sent\.  | Available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.High Throughput – By default, FIFO queues support up to 3,000 messages per second with [batching](sqs-batch-api-actions.md)\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/v1?#/case/create)\. FIFO queues support up to 300 messages per second \(300 send, receive, or delete operations per second\) without batching\.**Exactly\-Once Processing** – A message is delivered once and remains available until a consumer processes and deletes it\. Duplicates aren't introduced into the queue\.**First\-In\-First\-Out Delivery** – The order in which messages are sent and received is strictly preserved\. | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-standard-queue-diagram.png)  |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-fifo-queue-diagram.png)  | 
|  Send data between applications when the throughput is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  |  Send data between applications when the order of events is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  | 

## We Want to Hear from You<a name="sqs-we-want-to-hear-from-you"></a>

We welcome your feedback\. To contact us, visit the [Amazon SQS Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.