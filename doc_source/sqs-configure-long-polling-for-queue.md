# Tutorial: Configuring Long Polling for an Amazon SQS Queue<a name="sqs-configure-long-polling-for-queue"></a>

*Long polling* helps reduce the cost of using Amazon SQS by eliminating the number of empty responses \(when there are no messages available for a `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` request\) and false empty responses \(when messages are available but aren't included in a response\)\. The following example demonstrates how to configure long polling for an Amazon SQS queue\. For more information, see [Amazon SQS Long Polling](sqs-long-polling.md)\.


+ [Configure Long Polling for an Existing Amazon SQS Queue Using the AWS Management Console](#sqs-configure-long-polling-for-queue-console)
+ [AWS SDK for Java](#configure-long-polling-for-queue-java)

## AWS Management Console<a name="sqs-configure-long-polling-for-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.

1. **Standard** is selected by default\. Choose **FIFO**\.

1. Choose **Configure Queue**\.

1. For **Receive Message Wait Time**, type a number between `1` and `20`\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configuring-long-polling.png)
**Note**  
Setting the value to `0` configures *short polling*\. For more information, see [Differences Between Long and Short Polling](sqs-long-polling.md#sqs-short-long-polling-differences)\.

1. Choose **Create Queue**\.

   Your new queue is configured to use long polling, created, and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

## AWS SDK for Java<a name="configure-long-polling-for-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To configure long polling<a name="configure-long-polling-for-queue-java-api"></a>

#### Prerequisites<a name="configure-long-polling-for-queue-java-api-prerequisites"></a>

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

#### SQSLongPollingExample\.java<a name="configure-long-polling-example-java-code"></a>

The following example Java code creates a standard queue and configures long polling for it\.

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
import com.amazonaws.services.sqs.model.CreateQueueRequest;

import java.util.Scanner;

public class SQSLongPollingExample {

    //public static void main(String[] args) throws Exception{
    public static void main(String[] args) {

        final Scanner input = new Scanner(System.in);

        System.out.print("Enter the queue name: ");
        final String queueName = input.nextLine();

        System.out.print("Enter the ReceiveMessage wait time (1-20 seconds): ");
        final String receiveMessageWaitTime = input.nextLine();

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see
         * Creating Service Clients in the AWS SDK for Java Developer Guide.
         */
        final AmazonSQS sqs = AmazonSQSClientBuilder.defaultClient();

        try {
            // Create a queue with long polling.
            final CreateQueueRequest createQueueRequest = new CreateQueueRequest()
                    .withQueueName(queueName)
                    .addAttributesEntry("ReceiveMessageWaitTimeSeconds", receiveMessageWaitTime);
            sqs.createQueue(createQueueRequest);

            System.out.println("Created queue " + queueName + " with " +
                    "ReceiveMessage wait time set to " + receiveMessageWaitTime +
                    " seconds.");

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

### To configure long polling and send, receive, and delete a message<a name="configure-long-polling-send-receive-delete-message-java-api"></a>

1. Copy the example program for a [standard queue](standard-queues-getting-started-java.md) or a [FIFO queue](FIFO-queues-getting-started-java.md)\.

1. Retrieve the queue's URL:

   ```
   final String queueUrl = sqs.getQueueUrl(queueName).getQueueUrl();
   ```

1. Use the `SetQueueAttributesRequest` action to configure long polling for an existing queue:

   ```
   final SetQueueAttributesRequest setQueueAttributesRequest = new SetQueueAttributesRequest()
           .withQueueUrl(queueUrl)
           .addAttributesEntry("ReceiveMessageWaitTimeSeconds", "20");
   sqs.setQueueAttributes(setQueueAttributesRequest);
   ```

1. Use the `ReceiveMessageRequest` action to configure the long polling for a message receipt:

   ```
   final ReceiveMessageRequest receive_request = new ReceiveMessageRequest()
           .withQueueUrl(queueUrl)
           .withWaitTimeSeconds(20);
   sqs.receiveMessage(receive_request);
   ```

1. Compile and run your program\.

   The long\-polling for your queue is configured\.