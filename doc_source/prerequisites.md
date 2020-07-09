# Prerequisites<a name="prerequisites"></a>

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

  If you don't use Maven, you must add the `amazon-sqs-java-messaging-lib.jar` package to the Java class path\. For information about downloading the library, see [Amazon SQS Java Messaging Library](https://github.com/awslabs/amazon-sqs-java-messaging-lib)\.
**Note**  
The Amazon SQS Java Messaging Library includes support for [Maven](http://maven.apache.org/) and the [Spring Framework](http://projects.spring.io/spring-framework/)\.  
For code samples that use Maven, the Spring Framework, and the Amazon SQS Java Messaging Library, see [Working Java example for using JMS with Amazon SQS Standard queues](sqs-jms-code-examples.md)\.  

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
  + For information about creating a queue with Amazon SQS using either the AWS Management Console or the `CreateQueue` API, see [Creating a Queue](sqs-configure-create-queue.md)\.
  + For information about using the Amazon SQS Java Messaging Library, see [Getting started with the Amazon SQS Java Messaging Library](getting-started.md)\.