# Sending message attributes<a name="sqs-java-send-message-with-attributes"></a>

You can include structured metadata \(such as timestamps, geospatial data, signatures, and identifiers\) with messages using *message attributes*\. For more information, see [Amazon SQS message attributes](sqs-message-metadata.md#sqs-message-attributes)\.

 Before you run the example code, make sure that you have set your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-credentials.html) in the *AWS SDK for Java 2\.x Developer Guide*\. 

## Defining attributes<a name="sqs-java-define-attributes"></a>

To define an attribute for a message, add the following code, which uses the `[MessageAttributeValue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_MessageAttributeValue.html)` data type\. For more information, see [Message attribute components](sqs-message-metadata.md#message-attribute-components) and [Message attribute data types](sqs-message-metadata.md#message-attribute-data-types)\.

The AWS SDK for Java automatically calculates the message body and message attribute checksums and compares them with the data that Amazon SQS returns\. For more information, see the *[AWS SDK for Java 2\.x Developer Guide](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/)* and [Calculating the MD5 message digest for message attributes](sqs-message-metadata.md#sqs-attributes-md5-message-digest-calculation) for other programming languages\.

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
#### [ String \(custom\) ]

This example defines the custom attribute `String.EmployeeId` named `EmployeeId` with the value `ABC123456`\.

```
final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("EmployeeId", new MessageAttributeValue()
.withDataType("String.EmployeeId")
.withStringValue("ABC123456"));
```

------
#### [ Number \(custom\) ]

This example defines the custom attribute `Number.AccountId` named `AccountId` with the value `000123456`\.

```
final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("AccountId", new MessageAttributeValue()
.withDataType("Number.AccountId")
.withStringValue("000123456"));
```

**Note**  
Because the base data type is `Number`, the `[ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)` method returns `123456`\.

------
#### [ Binary \(custom\) ]

This example defines the custom attribute `Binary.JPEG` named `ApplicationIcon` with the value of an uninitialized 10\-byte array\.

```
final Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("ApplicationIcon", new MessageAttributeValue()
.withDataType("Binary.JPEG")
.withBinaryValue(ByteBuffer.wrap(new byte[10])));
```

------

## Sending a message with attributes<a name="sqs-java-send-attributes"></a>

This example adds the attributes to the `SendMessageRequest` before sending the message\.

```
// Send a message with an attribute.
final SendMessageRequest sendMessageRequest = new SendMessageRequest();
sendMessageRequest.withMessageBody("This is my message text.");
sendMessageRequest.withQueueUrl(myQueueUrl);
sendMessageRequest.withMessageAttributes(messageAttributes);
sqs.sendMessage(sendMessageRequest);
```

**Important**  
If you send a message to a First\-In\-First\-Out \(FIFO\) queue, make sure that the `sendMessage` method executes *after* you provide the message group ID\.  
If you use the `[SendMessageBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)` method instead of `[SendMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)`, you must specify message attributes for each message in the batch\.