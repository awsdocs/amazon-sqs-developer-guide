# Amazon SQS temporary queues<a name="sqs-temporary-queues"></a>

Temporary queues help you save development time and deployment costs when using common message patterns such as *request\-response*\. You can use the [Temporary Queue Client](https://github.com/awslabs/amazon-sqs-java-temporary-queues-client) to create high\-throughput, cost\-effective, application\-managed temporary queues\.

The client maps multiple *temporary queues*—application\-managed queues created on demand for a particular process—onto a single Amazon SQS queue automatically\. This allows your application to make fewer API calls and have a higher throughput when the traffic to each temporary queue is low\. When a temporary queue is no longer in use, the client cleans up the temporary queue automatically, even if some processes that use the client aren't shut down cleanly\.

The following are the benefits of temporary queues:
+ They serve as lightweight communication channels for specific threads or processes\.
+ They can be created and deleted without incurring additional cost\.
+ They are API\-compatible with static \(normal\) Amazon SQS queues\. This means that existing code that sends and receives messages can send messages to and receive messages from virtual queues\.

Topics
+ [Virtual queues](#virtual-queues)
+ [Request\-response messaging pattern \(virtual queues\)](#request-reply-messaging-pattern)
+ [Example scenario: Processing a login request](#example-scenario)
  + [On the client side](#process-login-request-client-side)
  + [On the server side](#process-login-request-server-side)
+ [Cleaning up queues](#cleaning-up-queues)

## Virtual queues<a name="virtual-queues"></a>

*Virtual queues* are local data structures that the Temporary Queue Client creates\. Virtual queues let you combine multiple low\-traffic destinations into a single Amazon SQS queue\. For best practices, see [Avoid reusing the same message group ID with virtual queues](using-messagegroupid-property.md#avoiding-reusing-message-group-id-with-virtual-queues)\.

**Note**  
Creating a virtual queue creates only temporary data structures for consumers to receive messages in\. Because a virtual queue makes no API calls to Amazon SQS, virtual queues incur no cost\.
TPS quotas apply to all virtual queues across a single host queue\. For more information, see [Quotas related to messages](quotas-messages.md)\.

The `AmazonSQSVirtualQueuesClient` wrapper class adds support for attributes related to virtual queues\. To create a virtual queue, you must call the `CreateQueue` API action using the `HostQueueURL` attribute\. This attribute specifies the existing queue that hosts the virtual queues\.

The URL of a virtual queue is in the following format\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue#MyVirtualQueueName
```

When a producer calls the `SendMessage` or `SendMessageBatch` API action on a virtual queue URL, the Temporary Queue Client does the following:

1. Extracts the virtual queue name\.

1. Attaches the virtual queue name as an additional message attribute\.

1. Sends the message to the host queue\.

While the producer sends messages, a background thread polls the host queue and sends received messages to virtual queues according to the corresponding message attributes\.

While the consumer calls the `ReceiveMessage` API action on a virtual queue URL, the Temporary Queue Client blocks the call locally until the background thread sends a message into the virtual queue\. \(This process is similar to message prefetching in the [Buffered Asynchronous Client](sqs-client-side-buffering-request-batching.md): a single API action can provide messages to up to 10 virtual queues\.\) Deleting a virtual queue removes any client\-side resources without calling Amazon SQS itself\.

The `AmazonSQSTemporaryQueuesClient` class turns all queues it creates into temporary queues automatically\. It also creates host queues with the same queue attributes automatically, on demand\. These queues' names share a common, configurable prefix \(by default, `__RequesterClientQueues__`\) that identifies them as temporary queues\. This allows the client to act as a drop\-in replacement that optimizes existing code which creates and deletes queues\. The client also includes the `AmazonSQSRequester` and `AmazonSQSResponder` interfaces that allow two\-way communication between queues\.

## Request\-response messaging pattern \(virtual queues\)<a name="request-reply-messaging-pattern"></a>

The most common use case for temporary queues is the *request\-response* messaging pattern, where a requester creates a *temporary queue* for receiving each response message\. To avoid creating an Amazon SQS queue for each response message, the Temporary Queue Client lets you create and delete multiple temporary queues without making any Amazon SQS API calls\. For more information, see [Implementing request\-response systems](working-with-messages.md#implementing-request-response-systems)\.

The following diagram shows a common configuration using this pattern\.

![\[A diagram of the request-response pattern used with Amazon SQS.\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-request-response-pattern.png)

## Example scenario: Processing a login request<a name="example-scenario"></a>

The following example scenario shows how you can use the `AmazonSQSRequester` and `AmazonSQSResponder` interfaces to process a user's login request\.

### On the client side<a name="process-login-request-client-side"></a>

```
public class LoginClient {

    // Specify the Amazon SQS queue to which to send requests.
    private final String requestQueueUrl;

    // Use the AmazonSQSRequester interface to create
    // a temporary queue for each response.
    private final AmazonSQSRequester sqsRequester = 
            AmazonSQSRequesterClientBuilder.defaultClient();

    private final LoginClient(String requestQueueUrl) {
        this.requestQueueUrl = requestQueueUrl;
    }

    // Send a login request.
    public String login(String body) throws TimeoutException {
        SendMessageRequest request = new SendMessageRequest()
                .withMessageBody(body)
                .withQueueUrl(requestQueueUrl);

        // If no response is received, in 20 seconds,
        // trigger the TimeoutException.
        Message reply = sqsRequester.sendMessageAndGetResponse(request, 
                20, TimeUnit.SECONDS);

        return reply.getBody();
    }
}
```

Sending a login request does the following:

1. Creates a temporary queue\.

1. Attaches the temporary queue's URL to the message as an attribute\.

1. Sends the message\.

1. Receives a response from the temporary queue\.

1. Deletes the temporary queue\.

1. Returns the response\.

### On the server side<a name="process-login-request-server-side"></a>

The following example assumes that, upon construction, a thread is created to poll the queue and call the `handleLoginRequest()` method for every message\. In addition, `doLogin()` is an assumed method\.

```
public class LoginServer {

    // Specify the Amazon SQS queue to poll for login requests.
    private final String requestQueueUrl;

    // Use the AmazonSQSResponder interface to take care
    // of sending responses to the correct response destination.
    private final AmazonSQSResponder sqsResponder = 
            AmazonSQSResponderClientBuilder.defaultClient();

    private final AmazonSQS(String requestQueueUrl) {
        this.requestQueueUrl = requestQueueUrl;
    }

    // Process login requests from the client.
    public void handleLoginRequest(Message message) {

        // Process the login and return a serialized result.
        String response = doLogin(message.getBody());

        // Extract the URL of the temporary queue from the message attribute
        // and send the response to the temporary queue.
        sqsResponder.sendResponseMessage(MessageContent.fromMessage(message), 
                new MessageContent(response));  
    }
}
```

## Cleaning up queues<a name="cleaning-up-queues"></a>

To ensure that Amazon SQS reclaims any in\-memory resources used by virtual queues, when your application no longer needs the Temporary Queue Client, it should call the `shutdown()` method\. You can also use the `shutdown()` method of the `AmazonSQSRequester` interface\.

The Temporary Queue Client also provides a way to eliminate orphaned host queues\. For each queue that receives an API call over a period of time \(by default, five minutes\), the client uses the `TagQueue` API action to tag a queue that remains in use\. 

**Note**  
Any API action taken on a queue marks it as non\-idle, including a `ReceiveMessage` action that returns no messages\.

The background thread uses the `ListQueues` and `ListTags` API actions to check all queues with the configured prefix, deleting any queues that haven't been tagged for at least five minutes\. In this way, if one client doesn't shut down cleanly, the other active clients clean up after it\. In order to reduce the duplication of work, all clients with the same prefix communicate through an shared, internal work queue named after the prefix\.