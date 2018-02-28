# Using JMS with Amazon SQS<a name="sqs-java-message-service-jms-client"></a>

The Amazon SQS Java Messaging Library is a JMS interface for Amazon SQS that lets you take advantage of Amazon SQS in applications that already use JMS\. The interface lets you use Amazon SQS as the JMS provider with minimal code changes\. Together with the AWS SDK for Java, the Amazon SQS Java Messaging Library lets you create JMS connections and sessions, as well as producers and consumers that send and receive messages to and from Amazon SQS queues\.

The library supports sending and receiving messages to a queue \(the JMS point\-to\-point model\) according to the [JMS 1\.1 specification](http://docs.oracle.com/javaee/6/api/javax/jms/package-summary.html)\. The library supports sending text, byte, or object messages synchronously to Amazon SQS queues\. The library also supports receiving objects synchronously or asynchronously\.

For information about features of the Amazon SQS Java Messaging Library that support the JMS 1\.1 specification, see [Supported JMS 1\.1 Implementations](supported-implementations.md) and the [Amazon SQS FAQs](https://aws.amazon.com/sqs/faqs/)\.


+ [Prerequisites](#prerequisites)
+ [Getting Started with the Amazon SQS Java Messaging Library](getting-started.md)
+ [Using the JMS Client with Other Amazon SQS Clients](sqs-jms-client-with-sqs-clients.md)
+ [Working Java Example for Using JMS with Amazon SQS Standard Queues](code-examples.md)
+ [Supported JMS 1\.1 Implementations](supported-implementations.md)

## Prerequisites<a name="prerequisites"></a>

Before you begin, you must have the following prerequisites:

+ **SDK for Java**

  There are two ways to include the SDK for Java in your project:

  + Download and install the SDK for Java\.

  + Use Maven to get the Amazon SQS Java Messaging Library\.
**Note**  
The SDK for Java is included as a dependency\.  
The [SDK for Java](https://aws.amazon.com/sdkforjava/) and Amazon SQS Extended Client Library for Java require the J2SE Development Kit 8\.0 or later\.

    For information about downloading the SDK for Java, see [SDK for Java](https://aws.amazon.com/sdkforjava/)\.

+ **Amazon SQS Java Messaging Library** 

  If you don't use Maven, you must add the package file `amazon-sqs-java-messaging-lib.jar` to the Java build class path\. For information about downloading the library, see [Amazon SQS Java Messaging Library](https://github.com/awslabs/amazon-sqs-java-messaging-lib)\.
**Note**  
The Amazon SQS Java Messaging Library includes support for [Maven](http://maven.apache.org/) and the [Spring Framework](http://projects.spring.io/spring-framework/)\.  
For code samples that use Maven, the Spring Framework, and the Amazon SQS Java Messaging Library, see [Working Java Example for Using JMS with Amazon SQS Standard Queues](code-examples.md)\.  

  ```
  <dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>amazon-sqs-java-messaging-lib</artifactId>
    <version>1.0.4</version>
    <type>jar</type>
  </dependency>
  ```

+ **Amazon SQS Queue**

  Create a queue using the AWS Management Console for Amazon SQS, the `CreateQueue` API, or the wrapped Amazon SQS client included in the Amazon SQS Java Messaging Library\.

  + For information about creating a queue with Amazon SQS using either the AWS Management Console or the `CreateQueue` API, see [Creating a Queue](sqs-create-queue.md)\.

  + For information about using the Amazon SQS Java Messaging Library, see [Getting Started with the Amazon SQS Java Messaging Library](getting-started.md)\.