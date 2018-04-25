# Tutorial: Sending a Message to an Amazon SQS Queue<a name="sqs-send-message"></a>

After you create your queue, you can send a message to it\. The following example demonstrates sending a message to an existing queue\.


+ [AWS Management Console](#send-message-console)
+ [AWS SDK for Java](#send-message-java)

## AWS Management Console<a name="send-message-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Send a Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The **Send a Message to *QueueName*** dialog box is displayed\.

   The following example shows the **Message Group ID** and **Message Deduplication ID** parameters specific to FIFO queues \([content\-based deduplication](FIFO-queues.md#FIFO-queues-exactly-once-processing) is disabled\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-dialog-box.png)

1. To send a message to a FIFO queue, type the **Message Body**, the **Message Group ID** `MyMessageGroupId1234567890`, and the **Message Deduplication ID** `MyMessageDeduplicationId1234567890`, and then choose **Send Message**\. For more information, see [FIFO Delivery Logic](FIFO-queues.md#FIFO-queues-understanding-logic)\.
**Note**  
The message group ID is always required\. However, if content\-based deduplication is enabled, the message deduplication ID is optional\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-button-fifo.png)

   Your message is sent and the **Send a Message to *QueueName*** dialog box is displayed, showing the attributes of the sent message\.

   The following example shows the **Sequence Number** attribute specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-message-attributes.png)

1. Choose **Close**\.

## AWS SDK for Java<a name="send-message-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To send a message to a standard queue<a name="send-message-standard-queue"></a>

1. Copy the [example program](standard-queues-getting-started-java.md)\.

   The following section of the code sends the `This is my message text.` message to your queue:

   ```
   // Send a message
   System.out.println("Sending a message to MyQueue.\n");
   sqs.sendMessage(new SendMessageRequest(myQueueUrl, "This is my message text."));
   ```

1. Compile and run the example\.

   The message is sent to the queue\. The response includes the following items:

   + The [message ID](sqs-general-identifiers.md#message-id) Amazon SQS assigns to the message\.

   + An MD5 digest of the message body, used to confirm that Amazon SQS received the message correctly \(for more information, see [RFC1321](http://faqs.org/rfcs/rfc1321.html)\)\.

   + The *request ID* that Amazon SQS assigned to your request\.

### To send a message to a FIFO queue<a name="send-message-fifo-queue"></a>

1. Copy the [example program](FIFO-queues-getting-started-java.md)\.

   The following section of the code sends the `This is my message text.` message to your queue:

   ```
   // Send a message
   System.out.println("Sending a message to MyFifoQueue.fifo.\n");
   final SendMessageRequest sendMessageRequest = new SendMessageRequest(myQueueUrl, "This is my message text.");
   
   // When you send messages to a FIFO queue, you must provide a non-empty MessageGroupId.
   sendMessageRequest.setMessageGroupId("messageGroup1");
   
   // Uncomment the following to provide the MessageDeduplicationId
   //sendMessageRequest.setMessageDeduplicationId("1");
   final SendMessageResult sendMessageResult = sqs.sendMessage(sendMessageRequest);
   final String sequenceNumber = sendMessageResult.getSequenceNumber();
   final String messageId = sendMessageResult.getMessageId();
   System.out.println("SendMessage succeed with messageId " + messageId + ", sequence number " + sequenceNumber 
           + "\n");
   ```

1. Compile and run the example\.

   The message is sent to your queue\.