# Standard Queues<a name="standard-queues"></a>

Amazon SQS offers *standard* as the default queue type\. Standard queues support a nearly unlimited number of transactions per second \(TPS\) per API action\. Standard queues support at\-least\-once message delivery\. However, occasionally \(because of the highly distributed architecture that allows nearly unlimited throughput\), more than one copy of a message might be delivered out of order\. Standard queues provide best\-effort ordering which ensures that messages are generally delivered in the same order as they're sent\. 

You can use standard message queues in many scenarios, as long as your application can process messages that arrive more than once and out of order, for example:

+ **Decouple live user requests from intensive background work** – Let users upload media while resizing or encoding it\.

+ **Allocate tasks to multiple worker nodes** – Process a high number of credit card validation requests\.

+ **Batch messages for future processing** – Schedule multiple entries to be added to a database\.

For best practices of working with standard queues, see [General Recommendations](general-recommendations.md)\.


+ [Message Ordering](#standard-queues-message-order)
+ [At\-Least\-Once Delivery](#standard-queues-at-least-once-delivery)
+ [Consuming Messages Using Short Polling](#consuming-messages-using-short-polling)
+ [Working Java Example for Standard Queues](#standard-queues-getting-started-java)

## Message Ordering<a name="standard-queues-message-order"></a>

A standard queue makes a best effort to preserve the order of messages, but more than one copy of a message might be delivered out of order\. If your system requires that order be preserved, we recommend using a [*FIFO \(First\-In\-First\-Out\) queue*](FIFO-queues.md) or adding sequencing information in each message so you can reorder the messages when they're received\.

## At\-Least\-Once Delivery<a name="standard-queues-at-least-once-delivery"></a>

Amazon SQS stores copies of your messages on multiple servers for redundancy and high availability\. On rare occasions, one of the servers that stores a copy of a message might be unavailable when you receive or delete a message\.

If this occurs, the copy of the message isn't deleted on that unavailable server, and you might get that message copy again when you receive messages\. Design your applications to be *idempotent* \(they should not be affected adversely when processing the same message more than once\)\. 

## Consuming Messages Using Short Polling<a name="consuming-messages-using-short-polling"></a>

The process of consuming messages from a queue depends on whether you use short polling \(the default behavior\) or long polling\. For more information about long polling, see [Amazon SQS Long Polling](sqs-long-polling.md)\.

When you consume messages from a queue using short polling, Amazon SQS samples a subset of its servers \(based on a weighted random distribution\) and returns messages from only those servers\. Thus, a particular receive request might not return all of your messages\. However, if you have fewer than 1,000 messages in your queue, a subsequent request will return your messages\. If you keep consuming from your queues, Amazon SQS samples all of its servers, and you receive all of your messages\.

The following figure shows the short\-polling behavior of messages returned from a standard queue after one of your system components makes a receive request\. Amazon SQS samples several of its servers \(in gray\) and returns messages A, C, D, and B from these servers\. Message E isn't returned for this request, but is returned for a subsequent request\.

![\[Message Sampling using Short (Standard) Polling\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/ArchOverview_Receive.png)

## Working Java Example for Standard Queues<a name="standard-queues-getting-started-java"></a>

### Prerequisites<a name="standard-queue-example-prerequisites"></a>

Ensure that the `aws-java-sdk-sqs.jar` package is in your Java build class path\. The following example shows this dependency in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-sqs</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

### SQSSimpleJavaClientExample\.java<a name="FIFO-queue-example-java-code"></a>

The following example Java code creates a queue and sends, receives, and deletes a message\.

```
/*
 * Copyright 2010-2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License.
 * A copy of the License is located at
 *
 *  https://aws.amazon.com/apache2.0
 *
 * or in the "license" file accompanying this file. This file is distributed
 * on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either
 * express or implied. See the License for the specific language governing
 * permissions and limitations under the License.
 *
 */

import com.amazonaws.AmazonClientException;
import com.amazonaws.AmazonServiceException;
import com.amazonaws.services.sqs.AmazonSQS;
import com.amazonaws.services.sqs.AmazonSQSClientBuilder;
import com.amazonaws.services.sqs.model.*;

import java.util.List;
import java.util.Map.Entry;

/**
 * This sample demonstrates how to make basic requests to Amazon SQS using the
 * AWS SDK for Java.
 * <p>
 * Prerequisites: You must have a valid Amazon Web Services developer account,
 * and be signed up to use Amazon SQS. For more information about Amazon SQS,
 * see https://aws.amazon.com/sqs
 * <p>
 * Make sure that your credentials are located in ~/.aws/credentials
 */
public class SQSSimpleJavaClientExample {

    public static void main(String[] args) throws Exception {

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see
         * [Creating Service Clients](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/creating-clients.html) in the AWS SDK for Java Developer Guide.
         */
        final AmazonSQS sqs = AmazonSQSClientBuilder.defaultClient();

        System.out.println("===============================================");
        System.out.println("Getting Started with Amazon SQS Standard Queues");
        System.out.println("===============================================\n");

        try {
            // Create a queue
            System.out.println("Creating a new SQS queue called MyQueue.\n");
            final CreateQueueRequest createQueueRequest =
                    new CreateQueueRequest("MyQueue");
            final String myQueueUrl = sqs.createQueue(createQueueRequest)
                    .getQueueUrl();

            // List queues
            System.out.println("Listing all queues in your account.\n");
            for (final String queueUrl : sqs.listQueues().getQueueUrls()) {
                System.out.println("  QueueUrl: " + queueUrl);
            }
            System.out.println();

            // Send a message
            System.out.println("Sending a message to MyQueue.\n");
            sqs.sendMessage(new SendMessageRequest(myQueueUrl,
                    "This is my message text."));

            // Receive messages
            System.out.println("Receiving messages from MyQueue.\n");
            final ReceiveMessageRequest receiveMessageRequest =
                    new ReceiveMessageRequest(myQueueUrl);
            final List<Message> messages = sqs.receiveMessage(receiveMessageRequest)
                    .getMessages();
            for (final Message message : messages) {
                System.out.println("Message");
                System.out.println("  MessageId:     "
                        + message.getMessageId());
                System.out.println("  ReceiptHandle: "
                        + message.getReceiptHandle());
                System.out.println("  MD5OfBody:     "
                        + message.getMD5OfBody());
                System.out.println("  Body:          "
                        + message.getBody());
                for (final Entry<String, String> entry : message.getAttributes()
                        .entrySet()) {
                    System.out.println("Attribute");
                    System.out.println("  Name:  " + entry
                            .getKey());
                    System.out.println("  Value: " + entry
                            .getValue());
                }
            }
            System.out.println();

            // Delete the message
            System.out.println("Deleting a message.\n");
            final String messageReceiptHandle = messages.get(0).getReceiptHandle();
            sqs.deleteMessage(new DeleteMessageRequest(myQueueUrl,
                    messageReceiptHandle));

            // Delete the queue
            System.out.println("Deleting the test queue.\n");
            sqs.deleteQueue(new DeleteQueueRequest(myQueueUrl));
        } catch (final AmazonServiceException ase) {
            System.out.println("Caught an AmazonServiceException, which means " +
                    "your request made it to Amazon SQS, but was " +
                    "rejected with an error response for some reason.");
            System.out.println("Error Message:    " + ase.getMessage());
            System.out.println("HTTP Status Code: " + ase.getStatusCode());
            System.out.println("AWS Error Code:   " + ase.getErrorCode());
            System.out.println("Error Type:       " + ase.getErrorType());
            System.out.println("Request ID:       " + ase.getRequestId());
        } catch (final AmazonClientException ace) {
            System.out.println("Caught an AmazonClientException, which means " +
                    "the client encountered a serious internal problem while " +
                    "trying to communicate with Amazon SQS, such as not " +
                    "being able to access the network.");
            System.out.println("Error Message: " + ace.getMessage());
        }
    }
}
```