# Resilience in Amazon SQS<a name="sqs-resilience"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Amazon SQS offers distributed queues\.

## Distributed queues<a name="resilience-distributed-queues"></a>

There are three main parts in a distributed messaging system: the components of your distributed system, your queue \(distributed on Amazon SQS servers\), and the messages in the queue\.

In the following scenario, your system has several *producers* \(components that send messages to the queue\) and *consumers* \(components that receive messages from the queue\)\. The queue \(which holds messages A through E\) redundantly stores the messages across multiple Amazon SQS servers\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview.png)