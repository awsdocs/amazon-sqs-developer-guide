# FIFO \(First\-In\-First\-Out\) Queues<a name="FIFO-queues"></a>

FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\. In addition to having all the capabilities of the standard queue, *FIFO \(First\-In\-First\-Out\)* queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated\. FIFO queues also provide exactly\-once processing but have a limited number of transactions per second \(TPS\):

+ FIFO queues support up to 300 messages per second \(300 send, receive, or delete operations per second\)\.

+ When you batch 10 messages per operation \(maximum\), FIFO queues can support up to 3,000 messages per second\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/v1?#/case/create)\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/XrX7rb6M3jw?rel=0&amp;controls=0&amp;showinfo=0)

FIFO queues are designed to enhance messaging between applications when the order of operations and events is critical, for example:

+ Ensure that user\-entered commands are executed in the right order\.

+ Display the correct product price by sending price modifications in the right order\.

+ Prevent a student from enrolling in a course before registering for an account\.

**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. The suffix counts towards the 80\-character queue name limit\. To determine whether a queue is FIFO, you can check whether the queue name ends with the suffix\.

For best practices of working with FIFO queues, see [Recommendations for FIFO \(First\-In\-First\-Out\) Queues ](FIFO-queue-recommendations.md) and [General Recommendations](general-recommendations.md)\.

For information about compatibility of clients and services with FIFO queues, see [Compatibility](#FIFO-compatibility)\.


+ [Message Ordering](#FIFO-queues-message-order)
+ [FIFO Queue Logic](#FIFO-queues-understanding-logic)
+ [Exactly\-Once Processing](#FIFO-queues-exactly-once-processing)
+ [Working Java Example for FIFO Queues](#FIFO-queues-getting-started-java)
+ [Moving from a Standard Queue to a FIFO Queue](#FIFO-queues-moving)
+ [Compatibility](#FIFO-compatibility)

## Message Ordering<a name="FIFO-queues-message-order"></a>

The FIFO queue improves upon and complements the standard queue\. The most important features of this queue type are *FIFO \(First\-In\-First\-Out\) delivery* and *exactly\-once processing*: The order in which messages are sent and received is strictly preserved and a message is delivered once and remains available until a consumer processes and deletes it; duplicates aren't introduced into the queue\. In addition, FIFO queues support *message groups* that allow multiple ordered message groups within a single queue\.

## FIFO Queue Logic<a name="FIFO-queues-understanding-logic"></a>

### Key Terms<a name="FIFO-key-terms"></a>

The following key terms can help you better understand the functionality of FIFO queues\. For detailed descriptions, see the *[Amazon Simple Queue Service API Reference](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Message Deduplication ID**  
The token used for deduplication of sent messages\. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5\-minute deduplication interval\.  
Message deduplication applies to an entire queue, not to individual message groups\.  
Amazon SQS continues to keep track of the message deduplication ID even after the message is received and deleted\.

**Message Group ID**  
The tag that specifies that a message belongs to a specific message group\. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group \(however, messages that belong to different message groups might be processed out of order\)\.

**Receive Request Attempt ID**  
The token used for deduplication of `ReceiveMessage` calls\.

**Sequence Number**  
The large, non\-consecutive number that Amazon SQS assigns to each message\.

### Sending Messages<a name="FIFO-sending-messages"></a>

If multiple messages are sent in succession to a FIFO queue, each with a distinct message deduplication ID, Amazon SQS stores the messages and acknowledges the transmission\. Then, each message can be received and processed in the exact order in which the messages were transmitted\.

In FIFO queues, messages are ordered based on message group ID\. If multiple hosts \(or different threads on the same host\) send messages with the same message group ID to a FIFO queue, Amazon SQS stores the messages in the order in which they arrive for processing\. To ensure that Amazon SQS preserves the order in which messages are sent and received, ensure that each producer uses a unique message group ID to send all its messages\.

FIFO queue logic applies only per message group ID\. Each message group ID represents a distinct ordered message group within an Amazon SQS queue\. For each message group ID, all messages are sent and received in strict order\. However, messages with different message group ID values might be sent and received out of order\. You must associate a message group ID with a message\. If you don't provide a message group ID, the action fails\. If you require a single group of ordered messages, provide the same message group ID for messages sent to the FIFO queue\.

### Receiving Messages<a name="FIFO-receiving-messages"></a>

You can't request to receive messages with a specific message group ID\.

When receiving messages from a FIFO queue with multiple message group IDs, Amazon SQS first attempts to return as many messages with the same message group ID as possible\. This allows other consumers to process messages with a different message group ID\.

**Note**  
It is possible to receive up to 10 messages in a single call using the `MaxNumberOfMessages` request parameter of the `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` API action\. These messages retain their FIFO order and can have the same message group ID\. Thus, if there are fewer than 10 messages available with the same message group ID, you might receive messages from another message group ID, in the same batch of 10 messages, but still in FIFO order\.

### Retrying Multiple Times<a name="FIFO-retrying-multiple-times"></a>

FIFO queues allow the producer or consumer to attempt multiple retries:

+ If the producer detects a failed `SendMessage` action, it can retry sending as many times as necessary, using the same message deduplication ID\. Assuming that the producer receives at least one acknowledgement before the deduplication interval expires, multiple retries neither affect the ordering of messages nor introduce duplicates\.

+ If the consumer detects a failed `ReceiveMessage` action, it can retry as many times as necessary, using the same receive request attempt ID\. Assuming that the consumer receives at least one acknowledgement before the visibility timeout expires, multiple retries don't affect the ordering of messages\.

+ When you receive a message with a message group ID, no more messages for the same message group ID are returned unless you delete the message or it becomes visible\.

## Exactly\-Once Processing<a name="FIFO-queues-exactly-once-processing"></a>

Unlike standard queues, FIFO queues don't introduce duplicate messages\. FIFO queues help you avoid sending duplicates to a queue\. If you retry the `SendMessage` action within the 5\-minute deduplication interval, Amazon SQS doesn't introduce any duplicates into the queue\.

To configure deduplication, you must do one of the following:

+ Enable content\-based deduplication\. This instructs Amazon SQS to use a SHA\-256 hash to generate the message deduplication ID using the body of the message—but not the attributes of the message\. For more information, see the documentation on the `[CreateQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`, `[GetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`, and `[SetQueueAttributes](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` actions in the *Amazon Simple Queue Service API Reference*\.

+ Explicitly provide the message deduplication ID \(or view the sequence number\) for the message\. For more information, see the documentation on the `[SendMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)`, `[SendMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)`, and `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` actions in the *Amazon Simple Queue Service API Reference*\.

## Working Java Example for FIFO Queues<a name="FIFO-queues-getting-started-java"></a>

### Prerequisites<a name="FIFO-queue-example-prerequisites"></a>

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

### SQSFIFOJavaClientExample\.java<a name="FIFO-queue-example-java-code"></a>

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

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;

public class SQSFIFOJavaClientExample {
    public static void main(String[] args) throws Exception {

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see
         * Creating Service Clients in the AWS SDK for Java Developer Guide.
         */
        final AmazonSQS sqs = AmazonSQSClientBuilder.defaultClient();

        System.out.println("===========================================");
        System.out.println("Getting Started with Amazon SQS FIFO Queues");
        System.out.println("===========================================\n");

        try {

            // Create a FIFO queue
            System.out.println("Creating a new Amazon SQS FIFO queue called " +
                    "MyFifoQueue.fifo.\n");
            final Map<String, String> attributes = new HashMap<String, String>();

            // A FIFO queue must have the FifoQueue attribute set to True
            attributes.put("FifoQueue", "true");

            /*
             * If the user doesn't provide a MessageDeduplicationId, generate a
             * MessageDeduplicationId based on the content.
             */
            attributes.put("ContentBasedDeduplication", "true");

            // The FIFO queue name must end with the .fifo suffix
            final CreateQueueRequest createQueueRequest =
                    new CreateQueueRequest("MyFifoQueue.fifo")
                            .withAttributes(attributes);
            final String myQueueUrl = sqs.createQueue(createQueueRequest).getQueueUrl();

            // List queues
            System.out.println("Listing all queues in your account.\n");
            for (final String queueUrl : sqs.listQueues().getQueueUrls()) {
                System.out.println("  QueueUrl: " + queueUrl);
            }
            System.out.println();

            // Send a message
            System.out.println("Sending a message to MyFifoQueue.fifo.\n");
            final SendMessageRequest sendMessageRequest =
                    new SendMessageRequest(myQueueUrl,
                            "This is my message text.");

            /*
             * When you send messages to a FIFO queue, you must provide a
             * non-empty MessageGroupId.
             */
            sendMessageRequest.setMessageGroupId("messageGroup1");

            // Uncomment the following to provide the MessageDeduplicationId
            //sendMessageRequest.setMessageDeduplicationId("1");
            final SendMessageResult sendMessageResult = sqs
                    .sendMessage(sendMessageRequest);
            final String sequenceNumber = sendMessageResult.getSequenceNumber();
            final String messageId = sendMessageResult.getMessageId();
            System.out.println("SendMessage succeed with messageId "
                    + messageId + ", sequence number " + sequenceNumber + "\n");

            // Receive messages
            System.out.println("Receiving messages from MyFifoQueue.fifo.\n");
            final ReceiveMessageRequest receiveMessageRequest =
                    new ReceiveMessageRequest(myQueueUrl);

            // Uncomment the following to provide the ReceiveRequestDeduplicationId
            //receiveMessageRequest.setReceiveRequestAttemptId("1");
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
                    System.out.println("  Name:  " + entry.getKey());
                    System.out.println("  Value: " + entry.getValue());
                }
            }
            System.out.println();

            // Delete the message
            System.out.println("Deleting the message.\n");
            final String messageReceiptHandle = messages.get(0).getReceiptHandle();
            sqs.deleteMessage(new DeleteMessageRequest(myQueueUrl,
                    messageReceiptHandle));

            // Delete the queue
            System.out.println("Deleting the queue.\n");
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

## Moving from a Standard Queue to a FIFO Queue<a name="FIFO-queues-moving"></a>

If you have an existing application that uses standard queues and you want to take advantage of the ordering or exactly\-once processing features of FIFO queues, you need to configure the queue and your application correctly\.

**Note**  
You can't convert an existing standard queue into a FIFO queue\. To make the move, you must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue\.

### Moving Checklist<a name="FIFO-queues-moving-checklist"></a>

Use the following checklist to ensure that your application works correctly with a FIFO queue\.

+ FIFO queues support up to 300 messages per second \(300 send, receive, or delete operations per second\)\. When you batch 10 messages per operation \(maximum\), FIFO queues can support up to 3,000 messages per second\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/v1?#/case/create)\.

+ FIFO queues don't support per\-message delays, only per\-queue delays\. If your application sets the same value of the `DelaySeconds` parameter on each message, you must modify your application to remove the per\-message delay and set `DelaySeconds` on the entire queue instead\.

+ Every message sent to a FIFO queue requires a message group ID\. If you don't need multiple ordered message groups, specify the same message group ID for all your messages\.

+ Before sending messages to a FIFO queue, confirm the following:

  + If your application can send messages with identical message bodies, you can modify your application to provide a unique message deduplication ID for each sent message\.

  + If your application sends messages with unique message bodies, you can enable content\-based deduplication\.

+ You don't have to make any code changes to your consumer\. However, if it takes a long time to process messages and your visibility timeout is set to a high value, consider adding a receive request attempt ID to each `ReceiveMessage` action\. This allows you to retry receive attempts in case of networking failures and prevents queues from pausing due to failed receive attempts\.

For more information, see the * [Amazon Simple Queue Service API Reference](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

## Compatibility<a name="FIFO-compatibility"></a>

### Clients<a name="FIFO-client-compatibility"></a>

The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

### Services<a name="FIFO-service-compatibility"></a>

If your application uses multiple AWS services, or a mix of AWS and external services, it is important to understand which service functionality doesn't support FIFO queues\.

Some AWS or external services that send notifications to Amazon SQS might not be compatible with FIFO queues, despite allowing you to set a FIFO queue as a target\.

The following features of AWS services aren't currently compatible with FIFO queues:

+ [Auto Scaling Lifecycle Hooks](http://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)

+ [AWS IoT Rule Actions](http://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html)

+ [AWS Lambda Dead\-Letter Queues](http://docs.aws.amazon.com/lambda/latest/dg/dlq.html)

For information about compatibility of other services with FIFO queues, see your service documentation\.