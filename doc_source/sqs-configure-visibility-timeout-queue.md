# Tutorial: Configuring Visibility Timeout for an Amazon SQS Queue<a name="sqs-configure-visibility-timeout-queue"></a>

Immediately after a message is received, it remains in the queue\. To prevent other consumers from processing the message again, Amazon SQS sets a *visibility timeout*, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message\. The default visibility timeout for a message is 30 seconds\. The maximum is 12 hours\. The following example shows how to configure visibility timeout for a queue using the AWS Management Console and for single or multiple messages using the AWS SDK for Java\. For more information, see [Amazon SQS Visibility Timeout](sqs-visibility-timeout.md)\.

**Note**  
You can use the AWS Management Console to configure visibility timeout only for queues, not for single or multiple messages\. To do this, you must use one of the AWS SDKs\. For more information, see the second Java example code below\.

**Topics**
+ [AWS Management Console](#sqs-configure-visibility-timeout-queue-console)
+ [AWS SDK for Java](#sqs-configure-visibility-timeout-queue-java)

## AWS Management Console<a name="sqs-configure-visibility-timeout-queue-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. Choose **Create New Queue\.**

1. On the **Create New Queue** page, ensure that you're in the correct region and then type the **Queue Name**\.
**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\. FIFO queues are available in the US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\), and EU \(Ireland\) regions\.

1. **Standard** is selected by default\. Choose **FIFO**\.

1. Choose **Configure Queue**\.

1. In this example, you set the default visibility timeout to 1 minute\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-visibility-timeout-queue-configure-parameters.png)

1. Choose **Create Queue**\.

   Your new queue is configured to use a 1\-minute visibility timeout, created, and selected in the queue list\.
**Note**  
When you create a queue, it can take a short time for the queue to propagate throughout Amazon SQS\.

   Your queue's **Default Visibility Timeout** is displayed on the **Details** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-configure-visibility-timeout-queue-default.png)

## AWS SDK for Java<a name="sqs-configure-visibility-timeout-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To configure visibility timeout for a queue<a name="configure-visibility-timeout-queue-java-api"></a>

#### Prerequisites<a name="configure-dead-letter-queue-java-api-prerequisites"></a>

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

#### SQSVisibilityTimeoutExample\.java<a name="configure-dead-letter-queue-java-api-code"></a>

The following example Java code creates a standard queue and sets the visibility timeout for it to 1 minute\.

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
	
	public class SQSVisibilityTimeoutExample {
	    public static void main(String[] args) {
	
	        final Scanner input = new Scanner(System.in);
	
	        System.out.print("Enter the queue name: ");
	        final String queueName = input.nextLine();
	
	        System.out.print("Enter the visibility timeout in seconds " +
	                "(0 seconds to 12 hours): ");
	        final String visibilityTimeout = input.nextLine();
	
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
	
	            // Set the visibility timeout for the queue.
	            final String queueUrl = sqs.getQueueUrl(queueName)
	                    .getQueueUrl();
	            final SetQueueAttributesRequest request = new SetQueueAttributesRequest()
	                    .withQueueUrl(queueUrl)
	                    .addAttributesEntry(QueueAttributeName.VisibilityTimeout
	                            .toString(), visibilityTimeout);
	            sqs.setQueueAttributes(request);
	
	            System.out.println("Created queue " + queueName + " with " +
	                    "visibility timeout set to " + visibilityTimeout +
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

### To configure visibility timeout for a single message or multiple messages and send, receive, and delete messages<a name="configure-visibility-timeout-queue-send-receive-delete-message-java-api"></a>

1. Copy the example program for a [standard queue](standard-queues-getting-started-java.md) or a [FIFO queue](FIFO-queues-getting-started-java.md)\.

1. To configure visibility timeout for a single message, pass the queue URL, the message receipt handle, and the visibility timeout value in seconds\.

   ```
   // Get the message receipt handle.
   	String receiptHandle = sqs.receiveMessage(myQueueUrl)
   	        .getMessages()
   	        .get(0)
   	        .getReceiptHandle();
   	
   	// Pass the queue URL, the message receipt handle, and the visibility timeout value.
   	sqs.changeMessageVisibility(myQueueUrl, receiptHandle, timeoutValue);
   ```

1. To configure visibility timeout for multiple messages \(for example, if you want to set different timeout values for different messages\), create an `ArrayList`, add messages to it with the visibility timeout value in seconds, and then pass the queue URL and the `ArrayList` of messages\.

   ```
   // Create an ArrayList for batched messages.
   	List<ChangeMessageVisibilityBatchRequestEntry> entries =
   	        new ArrayList<ChangeMessageVisibilityBatchRequestEntry>();
   	
   	// Add the first message to the ArrayList with a visibility timeout value.
   	entries.add(new ChangeMessageVisibilityBatchRequestEntry(
   	        "uniqueMessageId123", sqs.receiveMessage(myQueueUrl)
   	        .getMessages()
   	        .get(0)
   	        .getReceiptHandle())
   	        .withVisibilityTimeout(timeoutValue));
   	
   	// Add the second message to the ArrayList with a different timeout value. 
   	entries.add(new ChangeMessageVisibilityBatchRequestEntry(
   	        "uniqueMessageId456", sqs.receiveMessage(myQueueUrl)
   	        .getMessages()
   	        .get(0)
   	        .getReceiptHandle())
   	        .withVisibilityTimeout(timeoutValue + 60));
   	
   	sqs.changeMessageVisibilityBatch(myQueueUrl, entries);
   ```

1. Compile and run your program\.

   The visibility timeout for a single message or multiple messages is configured\.