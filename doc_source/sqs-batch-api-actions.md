# Amazon SQS Batch API Actions<a name="sqs-batch-api-actions"></a>

To reduce costs or manipulate up to 10 messages with a single API call, you can use the following batch API actions:

+ `[SendMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)`

+ `[DeleteMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessageBatch.html)`

+ `[ChangeMessageVisibilityBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibilityBatch.html)`

You can take advantage of batch functionality using the Query API, or an AWS SDK that supports the Amazon SQS batch actions\.

**Note**  
The total size of all messages that you send in a single `SendMessageBatch` call can't exceed 262,144 bytes \(256 KB\)\.  
You can't set permissions for `SendMessageBatch`, `DeleteMessageBatch`, or `ChangeMessageVisibilityBatch` explicitly\. Setting permissions for `SendMessage`, `DeleteMessage`, or `ChangeMessageVisibility` sets permissions for the corresponding batch versions of the actions\.  
The Amazon SQS console doesn't support batch API actions\.


+ [Enabling Client\-Side Buffering and Request Batching](#sqs-client-side-buffering-request-batching)
+ [Increasing Throughput using Horizontal Scaling and API Action Batching](#sqs-throughput-horizontal-scaling-and-batching)

## Enabling Client\-Side Buffering and Request Batching<a name="sqs-client-side-buffering-request-batching"></a>

The [AWS SDK for Java](https://aws.amazon.com/sdkforjava/) includes `AmazonSQSBufferedAsyncClient` which accesses Amazon SQS\. This client allows for simple request batching using client\-side buffering—calls made from the client are first buffered and then sent as a batch request to Amazon SQS\.

Client\-side buffering allows up to 10 requests to be buffered and sent as a batch request, decreasing your cost of using Amazon SQS and reducing the number of sent requests\. `AmazonSQSBufferedAsyncClient` buffers both synchronous and asynchronous calls\. Batched requests and support for [long polling](sqs-long-polling.md) can also help increase throughput\. For more information, see [Increasing Throughput using Horizontal Scaling and API Action Batching](#sqs-throughput-horizontal-scaling-and-batching)\.

Because `AmazonSQSBufferedAsyncClient` implements the same interface as `AmazonSQSAsyncClient`, migrating from `AmazonSQSAsyncClient` to `AmazonSQSBufferedAsyncClient` typically requires only minimal changes to your existing code\.

**Note**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

### Using AmazonSQSBufferedAsyncClient<a name="using-buffered-async-client"></a>

Before you begin, complete the steps in [Setting Up Amazon SQS](sqs-setting-up.md)\. 

You can create a new `AmazonSQSBufferedAsyncClient` based on `AmazonSQSAsyncClient`, for example:

```
// Create the basic Amazon SQS async client
final AmazonSQSAsync sqsAsync = new AmazonSQSAsyncClient();
 
// Create the buffered client
final AmazonSQSAsync bufferedSqs = new AmazonSQSBufferedAsyncClient(sqsAsync);
```

After you create the new `AmazonSQSBufferedAsyncClient`, you can make calls to it as you do with `AmazonSQSAsyncClient`, for example:

```
final CreateQueueRequest createRequest = new CreateQueueRequest().withQueueName("MyQueue");
 
final CreateQueueResult res = bufferedSqs.createQueue(createRequest);
 
final SendMessageRequest request = new SendMessageRequest();
final String body = "Your message text" + System.currentTimeMillis();
request.setMessageBody( body );
request.setQueueUrl(res.getQueueUrl());
 
final SendMessageResult sendResult = bufferedSqs.sendMessageAsync(request);
 
final ReceiveMessageRequest receiveRq = new ReceiveMessageRequest()
    .withMaxNumberOfMessages(1)
    .withQueueUrl(queueUrl);
final ReceiveMessageResult rx = bufferedSqs.receiveMessage(receiveRq);
```

### Configuring AmazonSQSBufferedAsyncClient<a name="configuring-buffered-async-client"></a>

`AmazonSQSBufferedAsyncClient` is preconfigured with settings that work for most use cases\. You can further configure `AmazonSQSBufferedAsyncClient`, for example:

1. Create an instance of the `QueueBufferConfig` class with the required configuration parameters\.

1. Provide the instance to the `AmazonSQSBufferedAsyncClient` constructor\.

```
// Create the basic Amazon SQS async client
final AmazonSQSAsync sqsAsync = new AmazonSQSAsyncClient();
 
final QueueBufferConfig config = new QueueBufferConfig()
    .withMaxInflightReceiveBatches(5)
    .withMaxDoneReceiveBatches(15);
 
// Create the buffered client
final AmazonSQSAsync bufferedSqs = new AmazonSQSBufferedAsyncClient(sqsAsync, config);
```


**QueueBufferConfig Configuration Parameters**  

| Parameter | Default Value | Description | 
| --- | --- | --- | 
| longPoll | true |  When `longPoll` is set to `true`, `AmazonSQSBufferedAsyncClient` attempts to use long polling when it consumes messages\.  | 
| longPollWaitTimeoutSeconds | 20 s |  The maximum amount of time \(in seconds\) which a `ReceiveMessage` call blocks off on the server, waiting for messages to appear in the queue before returning with an empty receive result\.  When long polling is disabled, this setting has no effect\.   | 
| maxBatchOpenMs | 200 ms |  The maximum amount of time \(in milliseconds\) that an outgoing call waits for other calls with which it batches messages of the same type\. The higher the setting, the fewer batches are required to perform the same amount of work \(however, the first call in a batch has to spend a longer time waiting\)\. When you set this parameter to `0`, submitted requests don't wait for other requests, effectively disabling batching\.  | 
| maxBatchSize | 10 requests per batch |  The maximum number of messages that are batched together in a single request\. The higher the setting, the fewer batches are required to carry out the same number of requests\.  10 requests per batch is the maximum allowed value for Amazon SQS\.   | 
| maxBatchSizeBytes | 256 KB |  The maximum size of a message batch, in bytes, that the client attempts to send to Amazon SQS\.  256 KB is the maximum allowed value for Amazon SQS\.   | 
| maxDoneReceiveBatches | 10 batches |  The maximum number of receive batches that `AmazonSQSBufferedAsyncClient` prefetches and stores client\-side\. The higher the setting, the more receive requests can be satisfied without having to make a call to Amazon SQS \(however, the more messages are prefetched, the longer they remain in the buffer, causing their own visibility timeout to expire\)\.  `0` indicates that all message prefetching is disabled and messages are consumed only on demand\.   | 
| maxInflightOutboundBatches | 5 batches |  The maximum number of active outbound batches that can be processed at the same time\. The higher the setting, the faster outbound batches can be sent \(subject to limits such as CPU or bandwidth\) and the more threads are consumed by `AmazonSQSBufferedAsyncClient`\.  | 
| maxInflightReceiveBatches | 10 batches |  The maximum number of active receive batches that can be processed at the same time\. The higher the setting, the more messages can be received \(subject to limits such as CPU or bandwidth\), and the more threads are consumed by `AmazonSQSBufferedAsyncClient`\.  `0` indicates that all message prefetching is disabled and messages are consumed only on demand\.   | 
| visibilityTimeoutSeconds | \-1 |  When this parameter is set to a positive, non\-zero value, the visibility timeout set here overrides the visibility timeout set on the queue from which messages are consumed\.  `-1` indicates that the default setting is selected for the queue\. You can't set visibility timeout to `0`\.   | 

## Increasing Throughput using Horizontal Scaling and API Action Batching<a name="sqs-throughput-horizontal-scaling-and-batching"></a>

Amazon SQS queues can deliver very high throughput\. Standard queues support a nearly unlimited number of transactions per second \(TPS\) per API action\. FIFO queues support up to 300 messages per second \(300 send, receive, or delete operations per second\)\. When you [batch](#sqs-batch-api-actions) 10 messages per operation \(maximum\), FIFO queues can support up to 3,000 messages per second\. To request a limit increase, [file a support request](https://console.aws.amazon.com/support/v1?#/case/create)\.

To achieve high throughput, you must scale message producers and consumers horizontally \(add more producers and consumers\)\.

### Horizontal Scaling<a name="horizontal-scaling"></a>

Because you access Amazon SQS through an HTTP request\-response protocol, the *request latency* \(the interval between initiating a request and receiving a response\) limits the throughput that you can achieve from a single thread using a single connection\. For example, if the latency from an Amazon EC2\-based client to Amazon SQS in the same region averages 20 ms, the maximum throughput from a single thread over a single connection averages 50 TPS\. 

*Horizontal scaling* involves increasing the number of message producers \(which make `[SendMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)` requests\) and consumers \(which make `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` and `[DeleteMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessage.html)` requests\) in order to increase your overall queue throughput\. You can scale horizontally in three ways:

+ Increase the number of threads per client

+ Add more clients

+ Increase the number of threads per client and add more clients

When you add more clients, you achieve essentially linear gains in queue throughput\. For example, if you double the number of clients, you also double the throughput\. 

**Note**  
As you scale horizontally, you must ensure that the Amazon SQS queue that you use has enough connections or threads to support the number of concurrent message producers and consumers that send requests and receive responses\. For example, by default, instances of the AWS SDK for Java `[AmazonSQSClient](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/sqs/AmazonSQSClient.html)` class maintain at most 50 connections to Amazon SQS\. To create additional concurrent producers and consumers, you must adjust the maximum number of allowable producer and consumer threads on an `AmazonSQSClientBuilder` object, for example:  

```
final AmazonSQS sqsClient = AmazonSQSClientBuilder.standard()
        .withClientConfiguration(new ClientConfiguration()
                .withMaxConnections(producerCount + consumerCount))
        .build();
```
For `[AmazonSQSAsyncClient](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/sqs/AmazonSQSAsyncClient.html)`, you also must make sure that enough threads are available\.

### API Action Batching<a name="request-batching"></a>

*Batching* performs more work during each round trip to the service \(for example, when you send multiple messages with a single `SendMessageBatch` request\)\. The Amazon SQS batch API actions are `[SendMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)`, `[DeleteMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessageBatch.html)`, and `[ChangeMessageVisibilityBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibilityBatch.html)`\. To take advantage of batching without changing your producers or consumers, you can use the [Amazon SQS Buffered Asynchronous Client](#sqs-client-side-buffering-request-batching)\.

**Note**  
Because `[ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` can process 10 messages at a time, there is no `ReceiveMessageBatch` action\.

Batching distributes the latency of the batch action over the multiple messages in a batch request, rather than accept the entire latency for a single message \(for example, a `[SendMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)` request\)\. Because each round trip carries more work, batch requests make more efficient use of threads and connections, improving throughput\.

You can combine batching with horizontal scaling to provide throughput with fewer threads, connections, and requests than individual message requests\. You can use batched Amazon SQS API actions to send, receive, or delete up to 10 messages at a time\. Because Amazon SQS charges by the request, batching can substantially reduce your costs\. 

Batching can introduce some complexity for your application \(for example, you application must accumulate messages before sending them, or it sometimes must wait longer for a response\)\. However, batching can be still effective in the following cases: 

+ Your application generates many messages in a short time, so the delay is never very long\. 

+ A message consumer fetches messages from a queue at its discretion, unlike typical message producers that need to send messages in response to events they don't control\. 

**Important**  
A batch request might succeed even though individual messages in the batch failed\. After a batch request, always check for individual message failures and retry the action if necessary\.

### Working Java Example for Single\-Operation and Batch Requests<a name="working-java-example-batch-requests"></a>

#### Prerequisites<a name="batch-request-java-example-prerequisites"></a>

Ensure that the `aws-java-sdk-sqs.jar`, `aws-java-sdk-ec2.jar`, and `commons-logging.jar` packages are in your Java build class path\. The following example shows these dependencies in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-sqs</artifactId>
        <version>LATEST</version>
    </dependency>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-ec2</artifactId>
        <version>LATEST</version>
    </dependency>
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

#### SimpleProducerConsumer\.java<a name="batch-request-java-example-code"></a>

The following Java code example implements a simple producer\-consumer pattern\. The main thread spawns a number of producer and consumer threads that process 1 KB messages for a specified time\. This example includes producers and consumers that make single\-operation requests and those that make batch requests\.

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
import com.amazonaws.ClientConfiguration;
import com.amazonaws.services.sqs.AmazonSQS;
import com.amazonaws.services.sqs.AmazonSQSClientBuilder;
import com.amazonaws.services.sqs.model.*;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

import java.math.BigInteger;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Scanner;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicBoolean;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * Start a specified number of producer and consumer threads, and produce-consume
 * for the least of the specified duration and 1 hour. Some messages can be left
 * in the queue because producers and consumers might not be in exact balance.
 */
public class SimpleProducerConsumer {

    // The maximum runtime of the program.
    private static final int MAX_RUNTIME_MINUTES = 60;
    private static Log log = LogFactory.getLog(SimpleProducerConsumer.class);

    public static void main(String[] args) throws InterruptedException {

        final Scanner input = new Scanner(System.in);

        System.out.print("Enter the queue name: ");
        final String queueName = input.nextLine();

        System.out.print("Enter the number of producers: ");
        final int producerCount = input.nextInt();

        System.out.print("Enter the number of consumers: ");
        final int consumerCount = input.nextInt();

        System.out.print("Enter the number of messages per batch: ");
        final int batchSize = input.nextInt();

        System.out.print("Enter the message size in bytes: ");
        final int messageSizeByte = input.nextInt();

        System.out.print("Enter the run time in minutes: ");
        final int runTimeMinutes = input.nextInt();

        /*
         * Create a new instance of the builder with all defaults (credentials
         * and region) set automatically. For more information, see Creating
         * Service Clients in the AWS SDK for Java Developer Guide.
         */
        final ClientConfiguration clientConfiguration = new ClientConfiguration()
                .withMaxConnections(producerCount + consumerCount);

        final AmazonSQS sqsClient = AmazonSQSClientBuilder.standard()
                .withClientConfiguration(clientConfiguration)
                .build();

        final String queueUrl = sqsClient
                .getQueueUrl(new GetQueueUrlRequest(queueName)).getQueueUrl();

        // The flag used to stop producer, consumer, and monitor threads.
        final AtomicBoolean stop = new AtomicBoolean(false);

        // Start the producers.
        final AtomicInteger producedCount = new AtomicInteger();
        final Thread[] producers = new Thread[producerCount];
        for (int i = 0; i < producerCount; i++) {
            if (batchSize == 1) {
                producers[i] = new Producer(sqsClient, queueUrl, messageSizeByte,
                        producedCount, stop);
            } else {
                producers[i] = new BatchProducer(sqsClient, queueUrl, batchSize,
                        messageSizeByte, producedCount,
                        stop);
            }
            producers[i].start();
        }

        // Start the consumers.
        final AtomicInteger consumedCount = new AtomicInteger();
        final Thread[] consumers = new Thread[consumerCount];
        for (int i = 0; i < consumerCount; i++) {
            if (batchSize == 1) {
                consumers[i] = new Consumer(sqsClient, queueUrl, consumedCount,
                        stop);
            } else {
                consumers[i] = new BatchConsumer(sqsClient, queueUrl, batchSize,
                        consumedCount, stop);
            }
            consumers[i].start();
        }

        // Start the monitor thread.
        final Thread monitor = new Monitor(producedCount, consumedCount, stop);
        monitor.start();

        // Wait for the specified amount of time then stop.
        Thread.sleep(TimeUnit.MINUTES.toMillis(Math.min(runTimeMinutes,
                MAX_RUNTIME_MINUTES)));
        stop.set(true);

        // Join all threads.
        for (int i = 0; i < producerCount; i++) {
            producers[i].join();
        }

        for (int i = 0; i < consumerCount; i++) {
            consumers[i].join();
        }

        monitor.interrupt();
        monitor.join();
    }

    private static String makeRandomString(int sizeByte) {
        final byte[] bs = new byte[(int) Math.ceil(sizeByte * 5 / 8)];
        new Random().nextBytes(bs);
        bs[0] = (byte) ((bs[0] | 64) & 127);
        return new BigInteger(bs).toString(32);
    }

    /**
     * The producer thread uses {@code SendMessage}
     * to send messages until it is stopped.
     */
    private static class Producer extends Thread {
        final AmazonSQS sqsClient;
        final String queueUrl;
        final AtomicInteger producedCount;
        final AtomicBoolean stop;
        final String theMessage;

        Producer(AmazonSQS sqsQueueBuffer, String queueUrl, int messageSizeByte,
                 AtomicInteger producedCount, AtomicBoolean stop) {
            this.sqsClient = sqsQueueBuffer;
            this.queueUrl = queueUrl;
            this.producedCount = producedCount;
            this.stop = stop;
            this.theMessage = makeRandomString(messageSizeByte);
        }

        /*
         * The producedCount object tracks the number of messages produced by
         * all producer threads. If there is an error, the program exits the
         * run() method.
         */
        public void run() {
            try {
                while (!stop.get()) {
                    sqsClient.sendMessage(new SendMessageRequest(queueUrl,
                            theMessage));
                    producedCount.incrementAndGet();
                }
            } catch (AmazonClientException e) {
                /*
                 * By default, AmazonSQSClient retries calls 3 times before
                 * failing. If this unlikely condition occurs, stop.
                 */
                log.error("Producer: " + e.getMessage());
                System.exit(1);
            }
        }
    }

    /**
     * The producer thread uses {@code SendMessageBatch}
     * to send messages until it is stopped.
     */
    private static class BatchProducer extends Thread {
        final AmazonSQS sqsClient;
        final String queueUrl;
        final int batchSize;
        final AtomicInteger producedCount;
        final AtomicBoolean stop;
        final String theMessage;

        BatchProducer(AmazonSQS sqsQueueBuffer, String queueUrl, int batchSize,
                      int messageSizeByte, AtomicInteger producedCount,
                      AtomicBoolean stop) {
            this.sqsClient = sqsQueueBuffer;
            this.queueUrl = queueUrl;
            this.batchSize = batchSize;
            this.producedCount = producedCount;
            this.stop = stop;
            this.theMessage = makeRandomString(messageSizeByte);
        }

        public void run() {
            try {
                while (!stop.get()) {
                    final SendMessageBatchRequest batchRequest =
                            new SendMessageBatchRequest().withQueueUrl(queueUrl);

                    final List<SendMessageBatchRequestEntry> entries =
                            new ArrayList<SendMessageBatchRequestEntry>();
                    for (int i = 0; i < batchSize; i++)
                        entries.add(new SendMessageBatchRequestEntry()
                                .withId(Integer.toString(i))
                                .withMessageBody(theMessage));
                    batchRequest.setEntries(entries);

                    final SendMessageBatchResult batchResult =
                            sqsClient.sendMessageBatch(batchRequest);
                    producedCount.addAndGet(batchResult.getSuccessful().size());

                    /*
                     * Because SendMessageBatch can return successfully, but
                     * individual batch items fail, retry the failed batch items.
                     */
                    if (!batchResult.getFailed().isEmpty()) {
                        log.warn("Producer: retrying sending "
                                + batchResult.getFailed().size() + " messages");
                        for (int i = 0, n = batchResult.getFailed().size();
                             i < n; i++) {
                            sqsClient.sendMessage(new
                                    SendMessageRequest(queueUrl, theMessage));
                            producedCount.incrementAndGet();
                        }
                    }
                }
            } catch (AmazonClientException e) {
                /*
                 * By default, AmazonSQSClient retries calls 3 times before
                 * failing. If this unlikely condition occurs, stop.
                 */
                log.error("BatchProducer: " + e.getMessage());
                System.exit(1);
            }
        }
    }

    /**
     * The consumer thread uses {@code ReceiveMessage} and {@code DeleteMessage}
     * to consume messages until it is stopped.
     */
    private static class Consumer extends Thread {
        final AmazonSQS sqsClient;
        final String queueUrl;
        final AtomicInteger consumedCount;
        final AtomicBoolean stop;

        Consumer(AmazonSQS sqsClient, String queueUrl, AtomicInteger consumedCount,
                 AtomicBoolean stop) {
            this.sqsClient = sqsClient;
            this.queueUrl = queueUrl;
            this.consumedCount = consumedCount;
            this.stop = stop;
        }

        /*
         * Each consumer thread receives and deletes messages until the main
         * thread stops the consumer thread. The consumedCount object tracks the
         * number of messages that are consumed by all consumer threads, and the
         * count is logged periodically.
         */
        public void run() {
            try {
                while (!stop.get()) {
                    try {
                        final ReceiveMessageResult result = sqsClient
                                .receiveMessage(new
                                        ReceiveMessageRequest(queueUrl));

                        if (!result.getMessages().isEmpty()) {
                            final Message m = result.getMessages().get(0);
                            sqsClient.deleteMessage(new
                                    DeleteMessageRequest(queueUrl,
                                    m.getReceiptHandle()));
                            consumedCount.incrementAndGet();
                        }
                    } catch (AmazonClientException e) {
                        log.error(e.getMessage());
                    }
                }
            } catch (AmazonClientException e) {
                /*
                 * By default, AmazonSQSClient retries calls 3 times before
                 * failing. If this unlikely condition occurs, stop.
                 */
                log.error("Consumer: " + e.getMessage());
                System.exit(1);
            }
        }
    }

    /**
     * The consumer thread uses {@code ReceiveMessage} and {@code
     * DeleteMessageBatch} to consume messages until it is stopped.
     */
    private static class BatchConsumer extends Thread {
        final AmazonSQS sqsClient;
        final String queueUrl;
        final int batchSize;
        final AtomicInteger consumedCount;
        final AtomicBoolean stop;

        BatchConsumer(AmazonSQS sqsClient, String queueUrl, int batchSize,
                      AtomicInteger consumedCount, AtomicBoolean stop) {
            this.sqsClient = sqsClient;
            this.queueUrl = queueUrl;
            this.batchSize = batchSize;
            this.consumedCount = consumedCount;
            this.stop = stop;
        }

        public void run() {
            try {
                while (!stop.get()) {
                    final ReceiveMessageResult result = sqsClient
                            .receiveMessage(new ReceiveMessageRequest(queueUrl)
                                    .withMaxNumberOfMessages(batchSize));

                    if (!result.getMessages().isEmpty()) {
                        final List<Message> messages = result.getMessages();
                        final DeleteMessageBatchRequest batchRequest =
                                new DeleteMessageBatchRequest()
                                        .withQueueUrl(queueUrl);

                        final List<DeleteMessageBatchRequestEntry> entries =
                                new ArrayList<DeleteMessageBatchRequestEntry>();
                        for (int i = 0, n = messages.size(); i < n; i++)
                            entries.add(new DeleteMessageBatchRequestEntry()
                                    .withId(Integer.toString(i))
                                    .withReceiptHandle(messages.get(i)
                                            .getReceiptHandle()));
                        batchRequest.setEntries(entries);

                        final DeleteMessageBatchResult batchResult = sqsClient
                                .deleteMessageBatch(batchRequest);
                        consumedCount.addAndGet(batchResult.getSuccessful().size());

                        /*
                         * Because DeleteMessageBatch can return successfully,
                         * but individual batch items fail, retry the failed
                         * batch items.
                         */
                        if (!batchResult.getFailed().isEmpty()) {
                            final int n = batchResult.getFailed().size();
                            log.warn("Producer: retrying deleting " + n
                                    + " messages");
                            for (BatchResultErrorEntry e : batchResult
                                    .getFailed()) {

                                sqsClient.deleteMessage(
                                        new DeleteMessageRequest(queueUrl,
                                                messages.get(Integer
                                                        .parseInt(e.getId()))
                                                        .getReceiptHandle()));

                                consumedCount.incrementAndGet();
                            }
                        }
                    }
                }
            } catch (AmazonClientException e) {
                /*
                 * By default, AmazonSQSClient retries calls 3 times before
                 * failing. If this unlikely condition occurs, stop.
                 */
                log.error("BatchConsumer: " + e.getMessage());
                System.exit(1);
            }
        }
    }

    /**
     * This thread prints every second the number of messages produced and
     * consumed so far.
     */
    private static class Monitor extends Thread {
        private final AtomicInteger producedCount;
        private final AtomicInteger consumedCount;
        private final AtomicBoolean stop;

        Monitor(AtomicInteger producedCount, AtomicInteger consumedCount,
                AtomicBoolean stop) {
            this.producedCount = producedCount;
            this.consumedCount = consumedCount;
            this.stop = stop;
        }

        public void run() {
            try {
                while (!stop.get()) {
                    Thread.sleep(1000);
                    log.info("produced messages = " + producedCount.get()
                            + ", consumed messages = " + consumedCount.get());
                }
            } catch (InterruptedException e) {
                // Allow the thread to exit.
            }
        }
    }
}
```

#### Monitoring Volume Metrics from the Example Run<a name="batch-request-java-example-monitoring-metrics"></a>

Amazon SQS automatically generates volume metrics for sent, received, and deleted messages\. You can access those metrics and others through the **Monitoring** tab for your queue or on the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/home)\.

**Note**  
The metrics can take up to 15 minutes after the queue starts to become available\.