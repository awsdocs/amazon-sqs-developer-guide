# Managing Large Amazon SQS Messages Using Amazon S3<a name="sqs-s3-messages"></a>

You can use Amazon S3 and the Amazon SQS Extended Client Library for Java to manage Amazon SQS messages\. This is especially useful for storing and consuming messages up to 2 GB in size\. You can use the Amazon SQS Extended Client Library for Java library to do the following:

+ Specify whether messages are always stored in Amazon S3 or only when the size of a message exceeds 256 KB\.

+ Send a message that references a single message object stored in an Amazon S3 bucket\. 

+ Get the corresponding message object from an Amazon S3 bucket\.

+ Delete the corresponding message object from an Amazon S3 bucket\.

**Note**  
The [SDK for Java](https://aws.amazon.com/sdkforjava/) and Amazon SQS Extended Client Library for Java require the J2SE Development Kit 8\.0 or later\.  
You can use the Amazon SQS Extended Client Library for Java to manage Amazon SQS messages using Amazon S3\. However, you can't do this using the AWS CLI, the Amazon SQS console, the Amazon SQS HTTP API, or any of the AWS SDKs—except for the SDK for Java\.

## Working Java Example for Using Amazon S3 for Large Amazon SQS Messages<a name="working-java-example-using-s3-for-large-sqs-messages"></a>

### Prerequisites<a name="working-java-example-using-s3-for-large-sqs-messages-prerequisites"></a>

Ensure that the `amazon-sqs-java-extended-client-lib.jar`, `aws-java-sdk-sqs.jar`, and `aws-java-sdk-s3.jar` packages are in your Java build class path\. The following example shows these dependencies in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>amazon-sqs-java-extended-client-lib</artifactId>
        <version>1.0.1</version>
    </dependency>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-sqs</artifactId>
        <version>LATEST</version>
    </dependency>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-s3</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

### SQSExtendedClientExample\.java<a name="working-java-example-using-s3-for-large-sqs-messages-example"></a>

The following example code creates an Amazon S3 bucket with a random name and adds a lifecycle rule to permanently delete objects after 14 days\. Next, the code creates a queue named `MyQueue` and sends a random message more than 256 KB in size to the queue; the message is stored in the Amazon S3 bucket\. Finally, the code consumes the message, returns information about the message, and deletes the message, the queue, and the bucket\.

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

import com.amazon.sqs.javamessaging.AmazonSQSExtendedClient;
import com.amazon.sqs.javamessaging.ExtendedClientConfiguration;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.*;
import com.amazonaws.services.sqs.AmazonSQS;
import com.amazonaws.services.sqs.AmazonSQSClientBuilder;
import com.amazonaws.services.sqs.model.*;
import org.joda.time.DateTime;
import org.joda.time.format.DateTimeFormat;

import java.util.Arrays;
import java.util.List;
import java.util.UUID;

public class SQSExtendedClientExample {

    // Create an Amazon S3 bucket with a random name.
    private static final String s3BucketName = UUID.randomUUID() + "-"
            + DateTimeFormat.forPattern("yyMMdd-hhmmss").print(new DateTime());

    public static void main(String[] args) {

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see 
         * [Creating Service Clients](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/creating-clients.html) in the AWS SDK for Java Developer Guide.
         */
        final AmazonS3 s3 = AmazonS3ClientBuilder.defaultClient();

        /*
         * Set the Amazon S3 bucket name, and then set a lifecycle rule on the
         * bucket to permanently delete objects 14 days after each object's
         * creation date.
         */
        final BucketLifecycleConfiguration.Rule expirationRule =
                new BucketLifecycleConfiguration.Rule();
        expirationRule.withExpirationInDays(14).withStatus("Enabled");
        final BucketLifecycleConfiguration lifecycleConfig =
                new BucketLifecycleConfiguration().withRules(expirationRule);

        // Create the bucket and allow message objects to be stored in the bucket.
        s3.createBucket(s3BucketName);
        s3.setBucketLifecycleConfiguration(s3BucketName, lifecycleConfig);
        System.out.println("Bucket created and configured.");

        /*
         * Set the Amazon SQS extended client configuration with large payload
         * support enabled.
         */
        final ExtendedClientConfiguration extendedClientConfig =
                new ExtendedClientConfiguration()
                        .withLargePayloadSupportEnabled(s3, s3BucketName);

        final AmazonSQS sqsExtended =
                new AmazonSQSExtendedClient(AmazonSQSClientBuilder
                        .defaultClient(), extendedClientConfig);

        /*
         * Create a long string of characters for the message object which will
         * be stored in the bucket.
         */
        int stringLength = 300000;
        char[] chars = new char[stringLength];
        Arrays.fill(chars, 'x');
        final String myLongString = new String(chars);

        // Create a message queue for this example.
        final String QueueName = "MyQueue" + UUID.randomUUID().toString();
        final CreateQueueRequest createQueueRequest =
                new CreateQueueRequest(QueueName);
        final String myQueueUrl = sqsExtended
                .createQueue(createQueueRequest).getQueueUrl();
        System.out.println("Queue created.");

        // Send the message.
        final SendMessageRequest myMessageRequest =
                new SendMessageRequest(myQueueUrl, myLongString);
        sqsExtended.sendMessage(myMessageRequest);
        System.out.println("Sent the message.");

        // Receive the message.
        final ReceiveMessageRequest receiveMessageRequest =
                new ReceiveMessageRequest(myQueueUrl);
        List<Message> messages = sqsExtended
                .receiveMessage(receiveMessageRequest).getMessages();

        // Print information about the message.
        for (Message message : messages) {
            System.out.println("\nMessage received.");
            System.out.println("  ID: " + message.getMessageId());
            System.out.println("  Receipt handle: " + message.getReceiptHandle());
            System.out.println("  Message body (first 5 characters): "
                    + message.getBody().substring(0, 5));
        }

        // Delete the message, the queue, and the bucket.
        final String messageReceiptHandle = messages.get(0).getReceiptHandle();
        sqsExtended.deleteMessage(new DeleteMessageRequest(myQueueUrl,
                messageReceiptHandle));
        System.out.println("Deleted the message.");

        sqsExtended.deleteQueue(new DeleteQueueRequest(myQueueUrl));
        System.out.println("Deleted the queue.");

        deleteBucketAndAllContents(s3);
        System.out.println("Deleted the bucket.");
    }

    private static void deleteBucketAndAllContents(AmazonS3 client) {

        ObjectListing objectListing = client.listObjects(s3BucketName);

        while (true) {
            for (S3ObjectSummary objectSummary : objectListing
                    .getObjectSummaries()) {
                client.deleteObject(s3BucketName, objectSummary.getKey());
            }

            if (objectListing.isTruncated()) {
                objectListing = client.listNextBatchOfObjects(objectListing);
            } else {
                break;
            }
        }

        final VersionListing list = client.listVersions(
                new ListVersionsRequest().withBucketName(s3BucketName));

        for (S3VersionSummary s : list.getVersionSummaries()) {
            client.deleteVersion(s3BucketName, s.getKey(), s.getVersionId());
        }

        client.deleteBucket(s3BucketName);
    }
}
```