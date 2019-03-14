# Tutorial: Sending a Message with Attributes to an Amazon SQS Queue<a name="sqs-send-message-with-attributes"></a>

You can include structured metadata \(such as timestamps, geospatial data, signatures, and identifiers\) with messages using *message attributes*\. In this tutorial you learn how to send a message with attributes to an existing queue\. For more information, see [Amazon SQS Message Attributes](sqs-message-attributes.md)\.

For a more detailed explanation of sending messages to standard and FIFO queues, see [Tutorial: Sending a Message to an Amazon SQS Queue](sqs-send-message.md)\.

**Topics**
+ [AWS Management Console](#send-message-with-attributes-console)
+ [AWS SDK for Java](#send-message-with-attributes-java)

## AWS Management Console<a name="send-message-with-attributes-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Send a Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The **Send a Message to *QueueName*** dialog box is displayed\.

   The following example shows the **Message Group ID** and **Message Deduplication ID** parameters specific to FIFO queues \([content\-based deduplication](FIFO-queues.md#FIFO-queues-exactly-once-processing) is disabled\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-dialog-box.png)

1. To send a message to a FIFO queue, type the **Message Body**, the **Message Group ID** `MyMessageGroupId1234567890`, and the **Message Deduplication ID** `MyMessageDeduplicationId1234567890`, and then choose **Message Attributes**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes.png)

1. Define the message attribute parameters\. For more information, see [Message Attribute Components](sqs-message-attributes.md#message-attribute-components) and [Message Attribute Data Types](sqs-message-attributes.md#message-attribute-data-types)\.

   1. For the message attribute **Name** type `MyMessageAttribute`\.

   1. For the message attribute data **Type**, select **Number** and type `byte` for the optional custom type\.

   1. For the message attribute **Value**, type `24`\.

   Choose **Add Attribute**\.

   The attribute is added to the message as **Number\.byte**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes-custom-attribute.png)

   You can modify the value before sending the message\. To delete the attribute, choose ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-delete-queue-tag.png)\.

1. When you finish adding attributes to the message, choose **Send Message**\.

   Your message is sent and the **Send a Message to *QueueName*** dialog box is displayed, showing the attributes of the sent message\.

   The following example shows the **MD5 of Message Attributes** specific to your custom message attribute and the **Sequence Number** attribute specific to FIFO queues\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes-custom-attribute-message-attributes.png)

1. Choose **Close**\.

## AWS SDK for Java<a name="send-message-with-attributes-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To send a message with attributes to a queue<a name="send-message-with-attributes-procedure"></a>

1. Copy the [standard queue example program](standard-queues-getting-started-java.md) or the [FIFO queue example program](FIFO-queues-getting-started-java.md)\.

1. To define an attribute for a message, add the following code which uses the `[MessageAttributeValue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_MessageAttributeValue.html)` data type\. For more information, see [Message Attribute Components](sqs-message-attributes.md#message-attribute-components) and [Message Attribute Data Types](sqs-message-attributes.md#message-attribute-data-types)\.
**Note**  
The AWS SDK for Java automatically calculates the message body and message attribute checksums and compares them with the data which Amazon SQS returns\. For more information, see the *[AWS SDK for Java Developer Guide](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/)* and [Calculating the MD5 Message Digest for Message Attributes](sqs-message-attributes.md#sqs-attributes-md5-message-digest-calculation) for other programming languages\.

------
#### [ String ]

   This example defines a `String` attribute named `Name` with the value `Jane`\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("Name", new MessageAttributeValue()
           .withDataType("String")
           .withStringValue("Jane"));
   ```

------
#### [ Number ]

   This example defines a `Number` attribute named `AccurateWeight` with the value `230.000000000000000001`\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("AccurateWeight", new MessageAttributeValue()
           .withDataType("Number")
           .withStringValue("230.000000000000000001"));
   ```

------
#### [ Binary ]

   This example defines a `Binary` attribute named `ByteArray` with the value of an uninitialized 10\-byte array\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("ByteArray", new MessageAttributeValue()
           .withDataType("Binary")
           .withBinaryValue(ByteBuffer.wrap(new byte[10])));
   ```

------
#### [ String \(Custom\) ]

   This example defines the custom attribute `String.EmployeeId` named `EmployeeId` with the value `ABC123456`\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("EmployeeId", new MessageAttributeValue()
           .withDataType("String.EmployeeId")
           .withStringValue("ABC123456"));
   ```

------
#### [ Number \(Custom\) ]

   This example defines the custom attribute `Number.AccountId` named `AccountId` with the value `0023456`\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("AccountId", new MessageAttributeValue()
           .withDataType("Number.AccountId")
           .withStringValue("000123456"));
   ```

**Note**  
Because the base data type is `Number`, the `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` action returns `123456`\.

------
#### [ Binary \(Custom\) ]

   This example defines the custom attribute `Binary.JPEG` named `ApplicationIcon` with the value of an uninitialized 10\-byte array\.

   ```
   final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
   messageAttributes.put("ApplicationIcon", new MessageAttributeValue()
           .withDataType("Binary.JPEG")
           .withBinaryValue(ByteBuffer.wrap(new byte[10])));
   ```

------

1. Replace the section of the code that sends the message with the following:

   ```
   // Send a message with an attribute
   final SendMessageRequest sendMessageRequest = new SendMessageRequest();
   sendMessageRequest.withMessageBody("This is my message text.");
   sendMessageRequest.withQueueUrl(myQueueUrl);
   sendMessageRequest.withMessageAttributes(messageAttributes);
   sqs.sendMessage(sendMessageRequest);
   ```
**Important**  
If you send a message to a FIFO queue, make sure that the `sendMessage` method executes *after* you provide the message group ID\.  
If you use the `[SendMessageBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)` action instead of `[SendMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)`, you must specify message attributes for each individual message in the batch\.

1. Compile and run the example\.

   The message is sent to the queue\. The response includes the following items:
   + The [message ID](sqs-general-identifiers.md#message-id) Amazon SQS assigns to the message\.
   + An MD5 digest of the message body, used to confirm that Amazon SQS received the message correctly \(for more information, see [RFC1321](http://faqs.org/rfcs/rfc1321.html)\)\.
   + An MD5 digest of the message attributes, used to confirm that Amazon SQS received the message attributes correctly\.
   + The *request ID* that Amazon SQS assigned to your request\.