# Enabling client\-side buffering and request batching<a name="sqs-client-side-buffering-request-batching"></a>

The [AWS SDK for Java](https://aws.amazon.com/sdkforjava/) includes `AmazonSQSBufferedAsyncClient` which accesses Amazon SQS\. This client allows for simple request batching using client\-side buffering—calls made from the client are first buffered and then sent as a batch request to Amazon SQS\.

Client\-side buffering allows up to 10 requests to be buffered and sent as a batch request, decreasing your cost of using Amazon SQS and reducing the number of sent requests\. `AmazonSQSBufferedAsyncClient` buffers both synchronous and asynchronous calls\. Batched requests and support for [long polling](sqs-short-and-long-polling.md) can also help increase throughput\. For more information, see [Increasing throughput using horizontal scaling and action batching](sqs-throughput-horizontal-scaling-and-batching.md)\.

Because `AmazonSQSBufferedAsyncClient` implements the same interface as `AmazonSQSAsyncClient`, migrating from `AmazonSQSAsyncClient` to `AmazonSQSBufferedAsyncClient` typically requires only minimal changes to your existing code\.

**Note**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

**Topics**
+ [Using AmazonSQSBufferedAsyncClient](#using-buffered-async-client)
+ [Configuring AmazonSQSBufferedAsyncClient](#configuring-buffered-async-client)

## Using AmazonSQSBufferedAsyncClient<a name="using-buffered-async-client"></a>

Before you begin, complete the steps in [Setting up Amazon SQS](sqs-setting-up.md)\. 

**Important**  
The AWS SDK for Java 2\.x isn't currently compatible with the `AmazonSQSBufferedAsyncClient`\.

You can create a new `AmazonSQSBufferedAsyncClient` based on `AmazonSQSAsyncClient`, for example:

```
// Create the basic Amazon SQS async client
final AmazonSQSAsync sqsAsync = new AmazonSQSAsyncClient();
 
// Create the buffered client
final AmazonSQSAsync bufferedSqs = new AmazonSQSBufferedAsyncClient(sqsAsync);
```

After you create the new `AmazonSQSBufferedAsyncClient`, you can use it to send multiple requests to Amazon SQS \(just as you can with `AmazonSQSAsyncClient`\), for example:

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

## Configuring AmazonSQSBufferedAsyncClient<a name="configuring-buffered-async-client"></a>

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


**QueueBufferConfig configuration parameters**  

| Parameter | Default value | Description | 
| --- | --- | --- | 
| longPoll | true |  When `longPoll` is set to `true`, `AmazonSQSBufferedAsyncClient` attempts to use long polling when it consumes messages\.  | 
| longPollWaitTimeoutSeconds | 20 s |  The maximum amount of time \(in seconds\) which a `ReceiveMessage` call blocks off on the server, waiting for messages to appear in the queue before returning with an empty receive result\.  When long polling is disabled, this setting has no effect\.   | 
| maxBatchOpenMs | 200 ms |  The maximum amount of time \(in milliseconds\) that an outgoing call waits for other calls with which it batches messages of the same type\. The higher the setting, the fewer batches are required to perform the same amount of work \(however, the first call in a batch has to spend a longer time waiting\)\. When you set this parameter to `0`, submitted requests don't wait for other requests, effectively disabling batching\.  | 
| maxBatchSize | 10 requests per batch |  The maximum number of messages that are batched together in a single request\. The higher the setting, the fewer batches are required to carry out the same number of requests\.  10 requests per batch is the maximum allowed value for Amazon SQS\.   | 
| maxBatchSizeBytes | 256 KB |  The maximum size of a message batch, in bytes, that the client attempts to send to Amazon SQS\.  256 KB is the maximum allowed value for Amazon SQS\.   | 
| maxDoneReceiveBatches | 10 batches |  The maximum number of receive batches that `AmazonSQSBufferedAsyncClient` prefetches and stores client\-side\. The higher the setting, the more receive requests can be satisfied without having to make a call to Amazon SQS \(however, the more messages are prefetched, the longer they remain in the buffer, causing their own visibility timeout to expire\)\.  `0` indicates that all message prefetching is disabled and messages are consumed only on demand\.   | 
| maxInflightOutboundBatches | 5 batches |  The maximum number of active outbound batches that can be processed at the same time\. The higher the setting, the faster outbound batches can be sent \(subject to quotas such as CPU or bandwidth\) and the more threads are consumed by `AmazonSQSBufferedAsyncClient`\.  | 
| maxInflightReceiveBatches | 10 batches |  The maximum number of active receive batches that can be processed at the same time\. The higher the setting, the more messages can be received \(subject to quotas such as CPU or bandwidth\), and the more threads are consumed by `AmazonSQSBufferedAsyncClient`\.  `0` indicates that all message prefetching is disabled and messages are consumed only on demand\.   | 
| visibilityTimeoutSeconds | \-1 |  When this parameter is set to a positive, non\-zero value, the visibility timeout set here overrides the visibility timeout set on the queue from which messages are consumed\.  `-1` indicates that the default setting is selected for the queue\. You can't set visibility timeout to `0`\.   | 