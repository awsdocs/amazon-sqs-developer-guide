# Tutorial: Configuring an Amazon SQS Dead\-Letter Queue<a name="sqs-configure-dead-letter-queue"></a>

A dead\-letter queue is a queue that *other* \(source\) queues can target for messages that can't be processed \(consumed\) successfully\. In this tutorial you learn how to create an Amazon SQS source queue and to configure a second queue as a dead\-letter queue for it\. For more information, see [Amazon SQS Dead\-Letter Queues](sqs-dead-letter-queues.md)\.

**Important**  
When you designate a queue to be a source queue, a dead\-letter queue is *not* created automatically\. You must first create a normal standard or FIFO queue before designating it a dead\-letter queue\. This tutorial assumes you already have a normal FIFO queue named `MyDeadLetterQueue.fifo`\.  
The dead\-letter queue of a FIFO queue must also be a FIFO queue\. Similarly, the dead\-letter queue of a standard queue must also be a standard queue\.

**Topics**
+ [AWS Management Console](#configure-dead-letter-queue-console)
+ [AWS SDK for Java](#configure-dead-letter-queue-java)

## AWS Management Console<a name="configure-dead-letter-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\.

1. **Standard** is selected by default\. Choose **FIFO**\.

1. Choose **Configure Queue**\.

1. In this example, you enable the redrive policy for your new queue, set the `MyDeadLetterQueue.fifo` queue as the dead\-letter queue, and set the number of maximum receives to `50`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-dead-letter-queue-configure-parameters.png)

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) To configure the dead\-letter queue, choose **Use Redrive Policy**\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) Enter the name of the existing **Dead Letter Queue** to which you want sources queues to send messages\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) To configure the number of times that a message can be received before being sent to a dead\-letter queue, set **Maximum Receives** to a value between 1 and 1,000\.
**Note**  
The **Maximum Receives** setting applies only to individual messages\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-4-red.png) Choose **Create Queue**\.

   Your new queue is configured to use a dead\-letter queue, created, and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

   Your queue's **Maximum Receives** and **Dead Letter Queue** ARN are displayed on the **Redrive Policy** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-creating-dead-letter-queue-redrive-policy.png)

## AWS SDK for Java<a name="configure-dead-letter-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To configure a dead\-letter queue<a name="configure-dead-letter-queue-java-api"></a>

#### Prerequisites<a name="configure-dead-letter-queue-java-api-prerequisites"></a>

Add the `aws-java-sdk-sqs.jar` package to your Java class path\. The following example shows this dependency in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-sqs</artifactId>
        <version><replaceable>LATEST</replaceable></version>
    </dependency>
</dependencies>
```

#### SQSDeadLetterQueueExample\.java<a name="configure-dead-letter-queue-java-code"></a>

The following example Java code creates two standard queues and configures one queue to act as a source queue for the other—a dead\-letter queue\.

```
/*
 * Copyright 2010-2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.
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
import com.amazonaws.services.sqs.model.GetQueueAttributesRequest;
import com.amazonaws.services.sqs.model.GetQueueAttributesResult;
import com.amazonaws.services.sqs.model.QueueAttributeName;
import com.amazonaws.services.sqs.model.SetQueueAttributesRequest;

import java.util.Scanner;

public class SQSDeadLetterQueueExample {
    public static void main(String[] args) {

        final Scanner input = new Scanner(System.in);

        System.out.print("Enter the source queue name: ");
        final String sourceQueueName = input.nextLine();

        System.out.print("Enter the dead-letter queue name: ");
        final String deadLetterQueueName = input.nextLine();

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see
         * Creating Service Clients in the AWS SDK for Java Developer Guide.
         */
        final AmazonSQS sqs = AmazonSQSClientBuilder.defaultClient();

        try {
            // Create a source queue.
            sqs.createQueue(sourceQueueName);

            // Create a dead-letter queue.
            sqs.createQueue(deadLetterQueueName);

            // Get the dead-letter queue ARN.
            final String deadLetterQueueUrl = sqs.getQueueUrl(deadLetterQueueName)
                    .getQueueUrl();
            final GetQueueAttributesResult deadLetterQueueAttributes = sqs.getQueueAttributes(
                    new GetQueueAttributesRequest(deadLetterQueueUrl)
                            .withAttributeNames("QueueArn"));
            final String deadLetterQueueArn = deadLetterQueueAttributes.getAttributes().get("QueueArn");

            // Set the dead letter queue for the source queue using the redrive policy.
            final String sourceQueueUrl = sqs.getQueueUrl(sourceQueueName)
                    .getQueueUrl();
            final SetQueueAttributesRequest request = new SetQueueAttributesRequest()
                    .withQueueUrl(sourceQueueUrl)
                    .addAttributesEntry(QueueAttributeName.RedrivePolicy.toString(),
                            "{\"maxReceiveCount\":\"5\", \"deadLetterTargetArn\":\""
                                    + deadLetterQueueArn + "\"}");
            sqs.setQueueAttributes(request);

            System.out.println("Set queue " + sourceQueueName + " as source queue " +
                    "for dead-letter queue " + deadLetterQueueName + ".");

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

### To configure a dead\-letter queue and send, receive, and delete a message<a name="configure-dead-letter-queue-send-receive-delete-message-java-api"></a>

1. Copy the example program for a [standard queue](standard-queues-getting-started-java.md) or a [FIFO queue](FIFO-queues-getting-started-java.md)\.

1. Set a string that contains JSON\-formatted parameters and values for the `RedrivePolicy` queue attribute:

   ```
   final String redrivePolicy = 
           "{\"maxReceiveCount\":\"5\", \"deadLetterTargetArn\":\"arn:aws:sqs:us-east-2:123456789012:MyDeadLetterQueue\"}";
   ```

1. Use the `CreateQueue` or `SetQueueAttributesRequest` action to configure the `RedrivePolicy` queue attribute:

   ```
   final SetQueueAttributesRequest queueAttributes = new SetQueueAttributesRequest();
   final Map<String,String> attributes = new HashMap<String,String>();            
   attributes.put("RedrivePolicy", redrivePolicy);            
   queueAttributes.setAttributes(attributes);
   queueAttributes.setQueueUrl(myQueueUrl);
   sqs.setQueueAttributes(queueAttributes);
   ```

1. Compile and run your program\.

   The dead\-letter queue is configured and the message is sent, received, and deleted\.