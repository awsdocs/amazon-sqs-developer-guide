# Basic Amazon SQS Architecture<a name="sqs-basic-architecture"></a>

There are three main actors in the overall system:

+ The components of your distributed system

+ Queues

+ Messages in the queues

In the following diagram, your system has several components that send messages to the queue and receive messages from the queue\. The diagram shows that a single queue, which has its messages \(A\-E\), is redundantly saved across multiple Amazon SQS servers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview.png)