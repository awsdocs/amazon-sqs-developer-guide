# Tutorial: Configuring an Amazon SQS Delay Queue<a name="sqs-configure-delay-queue"></a>

Delay queues let you postpone the delivery of new messages to a queue for a number of seconds\. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period\. The default \(minimum\) delay for a queue is 0 seconds\. The maximum is 15 minutes\. In this tutorial you learn how to configure a delay queue using the AWS Management Console or using the AWS SDK for Java\. For more information, see [Amazon SQS Delay Queues](sqs-delay-queues.md)\.

**Topics**
+ [AWS Management Console](#sqs-configure-delay-queue-console)
+ [AWS SDK for Java](#sqs-configure-delay-queue-java)
+ [To configure a delay queue and send, receive, and delete messages](#configure-delay-queue-send-receive-delete-message-java-api)

## AWS Management Console<a name="sqs-configure-delay-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\.

1. **Standard** is selected by default\. Choose **FIFO**\.

1. Choose **Configure Queue**\.

1. In this example, you set the delivery delay to 1 minute\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-delay-queue-configure-parameters.png)

1. Choose **Create Queue**\.

   Your new queue is configured to use a 1\-minute delay, created, and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

   Your queue's **Delivery Delay** is displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-delay-queue-default.png)

## AWS SDK for Java<a name="sqs-configure-delay-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To configure a delay queue<a name="configure-delay-queue-java-api"></a>

#### Prerequisites<a name="configure-delay-queue-java-api-prerequisites"></a>

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

#### SQSDelayQueueExample\.java<a name="configure-dead-letter-queue-java-api-code"></a>

The following example Java code creates a standard queue and sets the delay for it to 1 minute\.

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
	import com.amazonaws.services.sqs.model.QueueAttributeName;
	import com.amazonaws.services.sqs.model.SetQueueAttributesRequest;
	
	import java.util.Scanner;
	
	public class SQSDelayQueueExample {
	    public static void main(String[] args) {
	
	        final Scanner input = new Scanner(System.in);
	
	        System.out.print("Enter the queue name: ");
	        final String queueName = input.nextLine();
	
	        System.out.print("Enter the delay in seconds (0 seconds to 15 minutes): ");
	        final String queueDelay = input.nextLine();
	
	        /*
	         * Create a new instance of the builder with all defaults (credentials
	         * and region) set automatically. For more information, see
	         * Creating Service Clients in the AWS SDK for Java Developer Guide.
	         */
	        final AmazonSQS sqs = AmazonSQSClientBuilder.defaultClient();
	
	        try {
	            // Create a queue.
	            final CreateQueueRequest createQueueRequest = new CreateQueueRequest()
	                    .withQueueName(queueName);
	            sqs.createQueue(createQueueRequest);
	
	            // Set the delay for the queue.
	            final String queueUrl = sqs.getQueueUrl(queueName)
	                    .getQueueUrl();
	            final SetQueueAttributesRequest request = new SetQueueAttributesRequest()
	                    .withQueueUrl(queueUrl)
	                    .addAttributesEntry(QueueAttributeName.DelaySeconds
	                            .toString(), queueDelay);
	            sqs.setQueueAttributes(request);
	
	            System.out.println("Created queue " + queueName + " with " +
	                    "delay set to " + queueDelay + " seconds.");
	
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

## To configure a delay queue and send, receive, and delete messages<a name="configure-delay-queue-send-receive-delete-message-java-api"></a>

1. Copy the example program for a [standard queue](standard-queues-getting-started-java.md) or a [FIFO queue](FIFO-queues-getting-started-java.md)\.

1. To configure a delay queue, pass the delay value in seconds\.

   ```
   // Set the delay for the queue.
   	final String queueUrl = sqs.getQueueUrl(queueName).getQueueUrl();
   	final SetQueueAttributesRequest request = new SetQueueAttributesRequest()
   	        .withQueueUrl(queueUrl)
   	        .addAttributesEntry(QueueAttributeName.DelaySeconds.toString(), queueDelay);
   	sqs.setQueueAttributes(request);
   ```

1. Compile and run your program\.

   The delay queue is configured and the message is sent, received, and deleted\.