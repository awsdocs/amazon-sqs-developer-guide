# Working with JMS and Amazon SQS<a name="sqs-java-message-service-jms-client"></a>

The Amazon SQS Java Messaging Library is a Java Message Service \(JMS\) interface for Amazon SQS that lets you take advantage of Amazon SQS in applications that already use JMS\. The interface lets you use Amazon SQS as the JMS provider with minimal code changes\. Together with the AWS SDK for Java, the Amazon SQS Java Messaging Library lets you create JMS connections and sessions, as well as producers and consumers that send and receive messages to and from Amazon SQS queues\.

The library supports sending and receiving messages to a queue \(the JMS point\-to\-point model\) according to the [JMS 1\.1 specification](http://docs.oracle.com/javaee/6/api/javax/jms/package-summary.html)\. The library supports sending text, byte, or object messages synchronously to Amazon SQS queues\. The library also supports receiving objects synchronously or asynchronously\.

For information about features of the Amazon SQS Java Messaging Library that support the JMS 1\.1 specification, see [Supported JMS 1\.1 implementations](supported-implementations.md) and the [Amazon SQS FAQs](https://aws.amazon.com/sqs/faqs/)\.

**Topics**
+ [Prerequisites](prerequisites.md)
+ [Getting started with the Amazon SQS Java Messaging Library](getting-started.md)
+ [Using the Amazon SQS Java Message Service \(JMS\) Client with other Amazon SQS clients](sqs-jms-client-with-sqs-clients.md)
+ [Working Java example for using JMS with Amazon SQS Standard queues](sqs-jms-code-examples.md)
+ [Supported JMS 1\.1 implementations](supported-implementations.md)