# Amazon SQS FIFO \(First\-In\-First\-Out\) queues<a name="FIFO-queues"></a>

FIFO queues have all the capabilities of the [standard queue](standard-queues.md)\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0)

*FIFO \(First\-In\-First\-Out\)* queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated\. Examples of situations where you might use FIFO queues include the following:
+ To make sure that user\-entered commands are run in the right order\.
+ To display the correct product price by sending price modifications in the right order\.
+ To prevent a student from enrolling in a course before registering for an account\.

FIFO queues also provide exactly\-once processing but have a limited number of transactions per second \(TPS\)\. For information on throughput quotas, see [Quotas related to messages](quotas-messages.md)\.

**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. The suffix counts towards the 80\-character queue name quota\. To determine whether a queue is [FIFO](#FIFO-queues), you can check whether the queue name ends with the suffix\.

Amazon SQS FIFO queues are available in all Regions where Amazon SQS is available\.

For information about how to create and configure queues using the Amazon SQS console, see [Creating an Amazon SQS queue \(console\)](sqs-configure-create-queue.md)\. For Java examples, see [Amazon SQS Java SDK examples](sqs-java-tutorials.md)\.

For best practices of working with FIFO queues, see [Additional recommendations for Amazon SQS FIFO queues](sqs-additional-fifo-queue-recommendations.md) and [Recommendations for Amazon SQS standard and FIFO queues](sqs-standard-fifo-queue-best-practices.md)\.

For information about compatibility of clients and services with FIFO queues, see [Compatibility](FIFO-compatibility.md)\.

**Topics**
+ [Message ordering](FIFO-queues-message-order.md)
+ [Key terms](FIFO-key-terms.md)
+ [FIFO delivery logic](FIFO-queues-understanding-logic.md)
+ [Exactly\-once processing](FIFO-queues-exactly-once-processing.md)
+ [Moving from a standard queue to a FIFO queue](FIFO-queues-moving.md)
+ [High throughput for FIFO queues](high-throughput-fifo.md)
+ [Compatibility](FIFO-compatibility.md)