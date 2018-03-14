# What is Amazon Simple Queue Service?<a name="Welcome"></a>

Amazon Simple Queue Service \(Amazon SQS\) offers a reliable, highly\-scalable hosted queue for storing messages as they travel between applications or microservices\. It moves data between distributed application components and helps you decouple these components\. Amazon SQS provides familiar middleware constructs such as dead\-letter queues and poison\-pill management\. It also provides a generic web services API and can be accessed by any programming language that the AWS SDK supports\. Amazon SQS supports both [standard](standard-queues.md) and [FIFO queues](FIFO-queues.md)\.


+ [What Can I Use Amazon SQS For?](#sqs-what-can-i-use-it-for)
+ [What Type of Queue Do I Need?](#sqs-queue-types)
+ [What Are the Main Features of Amazon SQS?](#sqs-features)
+ [What Is the Basic Architecture of Amazon SQS?](#sqs-architectural-overview)
+ [We Want to Hear from You](#sqs-we-want-to-hear-from-you)

## What Can I Use Amazon SQS For?<a name="sqs-what-can-i-use-it-for"></a>

Use Amazon SQS for cases such as the following:

+ **Decoupling the components of an application** – You have a queue of work items and want to track the successful completion of each item independently\. Amazon SQS tracks the ACK/FAIL results, so the application doesn't have to maintain a persistent checkpoint or cursor\. After a configured visibility timeout, Amazon SQS deletes acknowledged messages and redelivers failed messages\.

+ **Configuring individual message delay** – You have a job queue and you need to schedule individual jobs with a delay\. With standard queues, you can configure individual messages to have a delay of up to 15 minutes\.

+ **Dynamically increasing concurrency or throughput at read time** – You have a work queue and want to add more consumers until the backlog is cleared\. Amazon SQS requires no pre\-provisioning\.

+ **Scaling transparently** – You buffer requests and the load changes as a result of occasional load spikes or the natural growth of your business\. Because Amazon SQS can process each buffered request independently, Amazon SQS can scale transparently to handle the load without any provisioning instructions from you\.

## What Type of Queue Do I Need?<a name="sqs-queue-types"></a>


| Standard Queue | FIFO Queue | 
| --- | --- | 
|  Available in all regions\. **Unlimited Throughput** – Standard queues support a nearly unlimited number of transactions per second \(TPS\) per API action\. **At\-Least\-Once Delivery** – A message is delivered at least once, but occasionally more than one copy of a message is delivered\. **Best\-Effort Ordering** – Occasionally, messages might be delivered in an order different from which they were sent\.  | Available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.High Throughput – By default, FIFO queues support up to 3,000 messages per second with [batching](sqs-batch-api-actions.md)\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/v1?#/case/create)\. FIFO queues support up to 300 messages per second \(300 send, receive, or delete operations per second\) without batching\.**Exactly\-Once Processing** – A message is delivered once and remains available until a consumer processes and deletes it\. Duplicates aren't introduced into the queue\.**First\-In\-First\-Out Delivery** – The order in which messages are sent and received is strictly preserved\. | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-standard-queue-diagram.png)  |  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-what-is-sqs-fifo-queue-diagram.png)  | 
|  Send data between applications when the throughput is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/Welcome.html)  |  Send data between applications when the order of events is important, for example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/Welcome.html)  | 

## What Are the Main Features of Amazon SQS?<a name="sqs-features"></a>

Amazon SQS provides the following major features:

+ **Redundant infrastructure** – Standard queues support at\-least\-once message delivery, while FIFO queues support exactly\-once message processing\. Amazon SQS provides highly\-concurrent access to messages and high availability for producing and consuming messages\.

+ **Multiple producers and consumers** – Multiple parts of your system can send or receive messages at the same time\. Amazon SQS locks the message during processing, keeping other parts of your system from processing the message simultaneously\.

+ **Configurable settings per queue** – All of your queues don't have to be exactly alike\. For example, you can optimize one queue for messages that require a longer processing time than others\.

+ **Variable message size** – Your messages can be up to 262,144 bytes \(256 KB\) in size\. You can store the contents of larger messages using the Amazon Simple Storage Service \(Amazon S3\) or Amazon DynamoDB, with Amazon SQS holding a pointer to the Amazon S3 object\. For more information, see [Managing Amazon SQS Messages with Amazon S3](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/s3-messages.html)\. You can also split a large message into smaller ones\.

+ **Access control** – You control who can send messages to a queue, and who can receive messages from a queue\.

+ **Delay queues** – You can set a default delay on a queue, so that delivery of all enqueued messages is postponed for the specified duration\. You can set the delay value when you create a queue with `CreateQueue`, and you can update the value with `SetQueueAttributes`\. If you update the value, the new value affects only messages enqueued after the update\.

## What Is the Basic Architecture of Amazon SQS?<a name="sqs-architectural-overview"></a>

There are three main actors in the overall system:

+ The components of your distributed system

+ Queues

+ Messages in the queues

In the following diagram, your system has several components that send messages to the queue and receive messages from the queue\. The diagram shows that a single queue, which has its messages \(A\-E\), is redundantly saved across multiple Amazon SQS servers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview.png)

## We Want to Hear from You<a name="sqs-we-want-to-hear-from-you"></a>

We welcome your feedback\. To contact us, visit the [Amazon SQS Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=12)\.