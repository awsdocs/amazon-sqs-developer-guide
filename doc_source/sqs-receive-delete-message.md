# Tutorial: Receiving and Deleting a Message from an Amazon SQS Queue<a name="sqs-receive-delete-message"></a>

After you send a message into a queue, you can consume it from the queue\. When you request a message from a queue, you can't specify which message to get\. Instead, you specify the maximum number of messages \(up to 10\) that you want to get\.

**Note**  
Because Amazon SQS is a distributed system, a queue with very few messages might display an empty response to a receive request\. In this case, you can rerun the request to get your message\. Depending on your application's needs, you might have to use short or [long polling](sqs-long-polling.md) to receive messages\.

Amazon SQS doesn't automatically delete a message after receiving it for you, in case you don't successfully receive the message \(for example, the consumers can fail or lose connectivity\)\. To delete a message, you must send a separate request which acknowledges that you no longer need the message because you've successfully received and processed it\.

The following example demonstrates receiving and deleting a message\.


+ [AWS Management Console](#receive-delete-message-console)
+ [Java](#receive-delete-message-java)

## AWS Management Console<a name="receive-delete-message-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **View/Delete Messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-view-delete-messages.png)

   The **View/Delete Messages in *QueueName*** dialog box is displayed\.
**Note**  
The first time you take this action, an information screen is displayed\. To hide the screen, check the **Don't show this again** checkbox\.

1. Choose **Start Polling for messages\.**  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-start-polling-for-messages.png)

   Amazon SQS begins to poll the messages in the queue\. The dialog box displays a message from the queue\. A progress bar at the bottom of the dialog box displays the status of the message's visibility timeout\.

   The following example shows the **Message Group ID**, **Message Deduplication ID**, and **Sequence Number** columns specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-polling-process.png)

1. *Before* the visibility timeout expires, select the message that you want to delete and then choose **Delete *1* Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-select-message-to-delete.png)

   The **Delete Messages** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-receive-delete-message-confirm-deleting-messages.png)

1. Confirm that the message you want to delete is checked and choose **Yes, Delete Checked Messages**\.

   The selected message is deleted\.

   When the progress bar is filled in, the [visibility timeout](sqs-visibility-timeout.md) expires and the message becomes visible to consumers\.

1. Select **Close**\.

## Java<a name="receive-delete-message-java"></a>

To specify the message to delete, provide the [receipt handle](sqs-queue-message-identifiers.md#receipt-handle) that Amazon SQS returned when you received the message\. You can delete only one message per action\. To delete an entire queue, you must use the `DeleteQueue` action\. \(You can delete an entire queue even if the queue has messages in it\.\)

**Note**  
If you don't have the receipt handle for the message, you can call the `ReceiveMessage` action to receive the message again\. Each time you receive the message, you get a different receipt handle\. Use the latest receipt handle when using the `DeleteMessage` action\. Otherwise, your message might not be deleted from the queue\.

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To receive and delete a message from a standard queue<a name="receive-delete-message-java-standard"></a>

1. Copy the [example program](standard-queues.md#standard-queues-getting-started-java)\.

   The following section of the code receives a message from your queue:

   ```
   // Receive messages
   System.out.println("Receiving messages from MyQueue.\n");
   final ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest(myQueueUrl);
   final List<Message> messages = sqs.receiveMessage(receiveMessageRequest).getMessages();
   for (final Message message : messages) {
       System.out.println("Message");
       System.out.println("  MessageId:     " + message.getMessageId());
       System.out.println("  ReceiptHandle: " + message.getReceiptHandle());
       System.out.println("  MD5OfBody:     " + message.getMD5OfBody());
       System.out.println("  Body:          " + message.getBody());
       for (final Entry<String, String> entry : message.getAttributes().entrySet()) {
           System.out.println("Attribute");
           System.out.println("  Name:  " + entry.getKey());
           System.out.println("  Value: " + entry.getValue());
       }
   }
   System.out.println();
   ```

   The following section of the code deletes the message:

   ```
   // Delete the message
   System.out.println("Deleting a message.\n");
   final String messageReceiptHandle = messages.get(0).getReceiptHandle();
   sqs.deleteMessage(new DeleteMessageRequest(myQueueUrl, messageReceiptHandle));
   ```

1. Compile and run the example\.

   The queue is polled and returns 0 or more messages\. The example prints the following items:

   + The [message ID](sqs-queue-message-identifiers.md#message-id) that you received when you sent the message to the queue\.

   + The [receipt handle](sqs-queue-message-identifiers.md#receipt-handle) that you later use to delete the message\.

   + An MD5 digest of the message body \(for more information, see [RFC1321](http://faqs.org/rfcs/rfc1321.html)\)\.

   + The message body\.

   + The *request ID* that Amazon SQS assigned to your request

   If no messages are received in this particular call, the response includes only the request ID\.

   The message is deleted\. The response includes the request ID that Amazon SQS assigned to your request\.

### To receive and delete a message from a FIFO queue<a name="receive-delete-message-java-FIFO"></a>

1. Copy the [example program](FIFO-queues.md#FIFO-queues-getting-started-java)\.

   The following section of the code receives a message from your queue:

   ```
   // Receive messages
   System.out.println("Receiving messages from MyFifoQueue.fifo.\n");
   final ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest(myQueueUrl);
   
   // Uncomment the following to provide the ReceiveRequestDeduplicationId
   //receiveMessageRequest.setReceiveRequestAttemptId("1");
   final List<Message> messages = sqs.receiveMessage(receiveMessageRequest).getMessages();
   for (final Message message : messages) {
       System.out.println("Message");
       System.out.println("  MessageId:     " + message.getMessageId());
       System.out.println("  ReceiptHandle: " + message.getReceiptHandle());
       System.out.println("  MD5OfBody:     " + message.getMD5OfBody());
       System.out.println("  Body:          " + message.getBody());
       for (final Entry<String, String> entry : message.getAttributes().entrySet()) {
           System.out.println("Attribute");
           System.out.println("  Name:  " + entry.getKey());
           System.out.println("  Value: " + entry.getValue());
       }
   }
   System.out.println();
   ```

   The following section of the code deletes the message:

   ```
   // Delete the message
   System.out.println("Deleting the message.\n");
   final String messageReceiptHandle = messages.get(0).getReceiptHandle();
   sqs.deleteMessage(new DeleteMessageRequest(myQueueUrl, messageReceiptHandle));
   ```

1. Compile and run the example\.

   The message is received and deleted\.