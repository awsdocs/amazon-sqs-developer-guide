# Getting started with the Amazon SQS Java Messaging Library<a name="getting-started"></a>

To get started using the Java Message Service \(JMS\) with Amazon SQS, use the code examples in this section\. The following sections show how to create a JMS connection and a session, and how to send and receive a message\.

The wrapped Amazon SQS client object included in the Amazon SQS Java Messaging Library checks if an Amazon SQS queue exists\. If the queue doesn't exist, the client creates it\.

## Creating a JMS connection<a name="creating-connection"></a>

1. Create a connection factory and call the `createConnection` method against the factory\.

   ```
   // Create a new connection factory with all defaults (credentials and region) set automatically
   SQSConnectionFactory connectionFactory = new SQSConnectionFactory(
           new ProviderConfiguration(),
           AmazonSQSClientBuilder.defaultClient()
           );
    
   // Create the connection.
   SQSConnection connection = connectionFactory.createConnection();
   ```

   The `SQSConnection` class extends `javax.jms.Connection`\. Together with the JMS standard connection methods, `SQSConnection` offers additional methods, such as `getAmazonSQSClient` and `getWrappedAmazonSQSClient`\. Both methods let you perform administrative operations not included in the JMS specification, such as creating new queues\. However, the `getWrappedAmazonSQSClient` method also provides a wrapped version of the Amazon SQS client used by the current connection\. The wrapper transforms every exception from the client into an `JMSException`, allowing it to be more easily used by existing code that expects `JMSException` occurrences\.

1. You can use the client objects returned from `getAmazonSQSClient` and `getWrappedAmazonSQSClient` to perform administrative operations not included in the JMS specification \(for example, you can create an Amazon SQS queue\)\.

    If you have existing code that expects JMS exceptions, then you should use `getWrappedAmazonSQSClient`:
   + If you use `getWrappedAmazonSQSClient`, the returned client object transforms all exceptions into JMS exceptions\.
   + If you use `getAmazonSQSClient`, the exceptions are all Amazon SQS exceptions\.

## Creating an Amazon SQS queue<a name="creating-queue"></a>

The wrapped client object checks if an Amazon SQS queue exists\.

If a queue doesn't exist, the client creates it\. If the queue does exist, the function doesn't return anything\. For more information, see the "Create the queue if needed" section in the [TextMessageSender\.java](sqs-jms-code-examples.md#example-sender) example\.

### To create a standard queue<a name="creating-queue-standard"></a>

```
// Get the wrapped client
AmazonSQSMessagingClientWrapper client = connection.getWrappedAmazonSQSClient();
 
// Create an SQS queue named MyQueue, if it doesn't already exist
if (!client.queueExists("MyQueue")) {
    client.createQueue("MyQueue");
}
```

### To create a FIFO queue<a name="creating-queue-FIFO"></a>

```
// Get the wrapped client
AmazonSQSMessagingClientWrapper client = connection.getWrappedAmazonSQSClient();

// Create an Amazon SQS FIFO queue named MyQueue.fifo, if it doesn't already exist
if (!client.queueExists("MyQueue.fifo")) {
    Map<String, String> attributes = new HashMap<String, String>();
    attributes.put("FifoQueue", "true");
    attributes.put("ContentBasedDeduplication", "true");
    client.createQueue(new CreateQueueRequest().withQueueName("MyQueue.fifo").withAttributes(attributes));
}
```

**Note**  
The name of a FIFO queue must end with the `.fifo` suffix\.  
For more information about the `ContentBasedDeduplication` attribute, see [Exactly\-once processing](FIFO-queues.md#FIFO-queues-exactly-once-processing)\.

## Sending messages synchronously<a name="send-messages-synchronously"></a>

1. When the connection and the underlying Amazon SQS queue are ready, create a nontransacted JMS session with `AUTO_ACKNOWLEDGE` mode\.

   ```
   // Create the nontransacted session with AUTO_ACKNOWLEDGE mode
   Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
   ```

1. To send a text message to the queue, create a JMS queue identity and a message producer\.

   ```
   // Create a queue identity and specify the queue name to the session
   Queue queue = session.createQueue("MyQueue");
    
   // Create a producer for the 'MyQueue'
   MessageProducer producer = session.createProducer(queue);
   ```

1. Create a text message and send it to the queue\.
   + To send a message to a standard queue, you don't need to set any additional parameters\.

     ```
     // Create the text message
     TextMessage message = session.createTextMessage("Hello World!");
      
     // Send the message
     producer.send(message);
     System.out.println("JMS Message " + message.getJMSMessageID());
     ```
   + To send a message to a FIFO queue, you must set the message group ID\. You can also set a message deduplication ID\. For more information, see [Key terms](FIFO-queues.md#FIFO-key-terms)\.

     ```
     // Create the text message
     TextMessage message = session.createTextMessage("Hello World!");
     
     // Set the message group ID
     message.setStringProperty("JMSXGroupID", "Default");
     
     // You can also set a custom message deduplication ID
     // message.setStringProperty("JMS_SQS_DeduplicationId", "hello");
     // Here, it's not needed because content-based deduplication is enabled for the queue
     
     // Send the message
     producer.send(message);
     System.out.println("JMS Message " + message.getJMSMessageID());
     System.out.println("JMS Message Sequence Number " + message.getStringProperty("JMS_SQS_SequenceNumber"));
     ```

## Receiving messages synchronously<a name="receive-messages-synchronously"></a>

1. To receive messages, create a consumer for the same queue and invoke the `start` method\.

   You can call the `start` method on the connection at any time\. However, the consumer doesn't begin to receive messages until you call it\.

   ```
   // Create a consumer for the 'MyQueue'
   MessageConsumer consumer = session.createConsumer(queue);
   // Start receiving incoming messages
   connection.start();
   ```

1. Call the `receive` method on the consumer with a timeout set to 1 second, and then print the contents of the received message\.
   + After receiving a message from a standard queue, you can access the contents of the message\.

     ```
     // Receive a message from 'MyQueue' and wait up to 1 second
     Message receivedMessage = consumer.receive(1000);
      
     // Cast the received message as TextMessage and display the text
     if (receivedMessage != null) {
         System.out.println("Received: " + ((TextMessage) receivedMessage).getText());
     }
     ```
   + After receiving a message from a FIFO queue, you can access the contents of the message and other, FIFO\-specific message attributes, such as the message group ID, message deduplication ID, and sequence number\. For more information, see [Key terms](FIFO-queues.md#FIFO-key-terms)\.

     ```
     // Receive a message from 'MyQueue' and wait up to 1 second
     Message receivedMessage = consumer.receive(1000);
     
     // Cast the received message as TextMessage and display the text
     if (receivedMessage != null) {
         System.out.println("Received: " + ((TextMessage) receivedMessage).getText());
         System.out.println("Group id: " + receivedMessage.getStringProperty("JMSXGroupID"));
         System.out.println("Message deduplication id: " + receivedMessage.getStringProperty("JMS_SQS_DeduplicationId"));
         System.out.println("Message sequence number: " + receivedMessage.getStringProperty("JMS_SQS_SequenceNumber"));
     }
     ```

1. Close the connection and the session\.

   ```
   // Close the connection (and the session).
   connection.close();
   ```

The output looks similar to the following:

```
JMS Message ID:8example-588b-44e5-bbcf-d816example2
Received: Hello World!
```

**Note**  
You can use the Spring Framework to initialize these objects\.  
For additional information, see `SpringExampleConfiguration.xml`, `SpringExample.java`, and the other helper classes in `ExampleConfiguration.java` and `ExampleCommon.java` in the [Working Java example for using JMS with Amazon SQS Standard queues](sqs-jms-code-examples.md) section\.

For complete examples of sending and receiving objects, see [TextMessageSender\.java](sqs-jms-code-examples.md#example-sender) and [SyncMessageReceiver\.java](sqs-jms-code-examples.md#example-synchronous-message-receiver)\.

## Receiving messages asynchronously<a name="receive-messages-asynchronously"></a>

In the example in [Getting started with the Amazon SQS Java Messaging Library](#getting-started), a message is sent to `MyQueue` and received synchronously\.

The following example shows how to receive the messages asynchronously through a listener\.

1. Implement the `MessageListener` interface\.

   ```
   class MyListener implements MessageListener {
    
       @Override
       public void onMessage(Message message) {
           try {
               // Cast the received message as TextMessage and print the text to screen.
               System.out.println("Received: " + ((TextMessage) message).getText());
           } catch (JMSException e) {
               e.printStackTrace();
           }
       }
   }
   ```

   The `onMessage` method of the `MessageListener` interface is called when you receive a message\. In this listener implementation, the text stored in the message is printed\.

1. Instead of explicitly calling the `receive` method on the consumer, set the message listener of the consumer to an instance of the `MyListener` implementation\. The main thread waits for one second\.

   ```
   // Create a consumer for the 'MyQueue'.
   MessageConsumer consumer = session.createConsumer(queue);
    
   // Instantiate and set the message listener for the consumer.
   consumer.setMessageListener(new MyListener());
    
   // Start receiving incoming messages.
   connection.start();
    
   // Wait for 1 second. The listener onMessage() method is invoked when a message is received.
   Thread.sleep(1000);
   ```

The rest of the steps are identical to the ones in the [Getting started with the Amazon SQS Java Messaging Library](#getting-started) example\. For a complete example of an asynchronous consumer, see `AsyncMessageReceiver.java` in [Working Java example for using JMS with Amazon SQS Standard queues](sqs-jms-code-examples.md)\.

The output for this example looks similar to the following:

```
JMS Message ID:8example-588b-44e5-bbcf-d816example2
Received: Hello World!
```

## Using client acknowledge mode<a name="using-client-acknowledge-mode"></a>

The example in [Getting started with the Amazon SQS Java Messaging Library](#getting-started) uses `AUTO_ACKNOWLEDGE` mode where every received message is acknowledged automatically \(and therefore deleted from the underlying Amazon SQS queue\)\.

1. To explicitly acknowledge the messages after they're processed, you must create the session with `CLIENT_ACKNOWLEDGE` mode\.

   ```
   // Create the non-transacted session with CLIENT_ACKNOWLEDGE mode.
   Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
   ```

1. When the message is received, display it and then explicitly acknowledge it\.

   ```
   // Cast the received message as TextMessage and print the text to screen. Also acknowledge the message.
   if (receivedMessage != null) {
       System.out.println("Received: " + ((TextMessage) receivedMessage).getText());
       receivedMessage.acknowledge();
       System.out.println("Acknowledged: " + message.getJMSMessageID());
   }
   ```
**Note**  
In this mode, when a message is acknowledged, all messages received before this message are implicitly acknowledged as well\. For example, if 10 messages are received, and only the 10th message is acknowledged \(in the order the messages are received\), then all of the previous nine messages are also acknowledged\.

The rest of the steps are identical to the ones in the [Getting started with the Amazon SQS Java Messaging Library](#getting-started) example\. For a complete example of a synchronous consumer with client acknowledge mode, see `SyncMessageReceiverClientAcknowledge.java` in [Working Java example for using JMS with Amazon SQS Standard queues](sqs-jms-code-examples.md)\.

The output for this example looks similar to the following:

```
JMS Message ID:4example-aa0e-403f-b6df-5e02example5
Received: Hello World!
Acknowledged: ID:4example-aa0e-403f-b6df-5e02example5
```

## Using unordered acknowledge mode<a name="using-unordered-acknowledge-mode"></a>

When using `CLIENT_ACKNOWLEDGE` mode, all messages received before an explicitly\-acknowledged message are acknowledged automatically\. For more information, see [Using client acknowledge mode](#using-client-acknowledge-mode)\.

The Amazon SQS Java Messaging Library provides another acknowledgement mode\. When using `UNORDERED_ACKNOWLEDGE` mode, all received messages must be individually and explicitly acknowledged by the client, regardless of their reception order\. To do this, create a session with `UNORDERED_ACKNOWLEDGE` mode\.

```
// Create the non-transacted session with UNORDERED_ACKNOWLEDGE mode.
Session session = connection.createSession(false, SQSSession.UNORDERED_ACKNOWLEDGE);
```

The remaining steps are identical to the ones in the [Using client acknowledge mode](#using-client-acknowledge-mode) example\. For a complete example of a synchronous consumer with `UNORDERED_ACKNOWLEDGE` mode, see `SyncMessageReceiverUnorderedAcknowledge.java`\.

In this example, the output looks similar to the following:

```
JMS Message ID:dexample-73ad-4adb-bc6c-4357example7
Received: Hello World!
Acknowledged: ID:dexample-73ad-4adb-bc6c-4357example7
```