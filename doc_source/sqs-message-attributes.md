# Amazon SQS Message Attributes<a name="sqs-message-attributes"></a>

Amazon SQS provides support for *message attributes*\. Message attributes allow you to provide structured metadata items \(such as timestamps, geospatial data, signatures, and identifiers\) about the message\. Message attributes are optional and separate from, but sent along with, the message body\. This information can be used by the consumer of the message to help decide how to handle the message without having to first process the message body\. Each message can have up to 10 attributes\. To specify message attributes, you can use the AWS Management Console, AWS software development kits \(SDKs\), or Query API\. 


+ [Message Attribute Items and Validation](#message-attributes-items-validation)
+ [Message Attribute Data Types and Validation](#message-attributes-data-types-validation)
+ [Using Message Attributes with the AWS Management Console](#sqs-attrib)
+ [Using Message Attributes with the AWS SDKs](#sqs-attrib-java-sdk)
+ [Using Message Attributes with the Amazon SQS Query API](#sqs-attrib-api)
+ [MD5 Message\-Digest Calculation](#sqs-attrib-md5)

## Message Attribute Items and Validation<a name="message-attributes-items-validation"></a>

Each message attribute consists of the following items:

+ **Name** – The message attribute name can contain the following characters: `A`\-`Z`, `a`\-`z`, `0`\-`9`, underscore\(`_`\), hyphen\(`-`\), and period \(`.`\)\. The name must not start or end with a period, and it should not have successive periods\. The name is case\-sensitive and must be unique among all attribute names for the message\. The name can be up to 256 characters long\. The name can't start with `AWS.` or `Amazon.` \(or any variations in casing\) because these prefixes are reserved for use by Amazon Web Services\.

+ **Type** – The supported message attribute data types are String, Number, and Binary\. You can also provide custom information about the type\. The data type has the same restrictions on the content as the message body\. The data type is case\-sensitive, and it can be up to 256 bytes long\. For more information, see the [Message Attribute Data Types and Validation](#message-attributes-data-types-validation) section\.

+ **Value** – The user\-specified message attribute value\. For string data types, the value attribute has the same restrictions on the content as the message body\. For more information, see `[SendMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)` in the *Amazon Simple Queue Service API Reference*\.

Name, type, and value must not be empty or null\. In addition, the message body should not be empty or null\. All parts of the message attribute, including name, type, and value, are included in the message size restriction, which is currently 256 KB \(262,144 bytes\)\.

## Message Attribute Data Types and Validation<a name="message-attributes-data-types-validation"></a>

Message attribute data types identify how the message attribute values are handled by Amazon SQS\. For example, if the type is a number, Amazon SQS validates that it's a number\.

Amazon SQS supports the logical data types `Binary`, `Number`, and `String` with optional custom type labels in the format `.custom-type`\.

+ **Binary** – Binary type attributes can store any binary data, for example, compressed data, encrypted data, or images\.

+ **Number** – Numbers are positive or negative integers or floating point numbers\. Numbers have sufficient range and precision to encompass most of the possible values that integers, floats, and doubles typically support\. A number can have up to 38 digits of precision, and it can be between 10^\-128 and 10^\+126\. Leading and trailing zeroes are trimmed\.

+ **String** – Strings are Unicode with UTF\-8 binary encoding\. For more information, see [ASCII Printable Characters](http://en.wikipedia.org/wiki/ASCII#ASCII_printable_characters)\.

You can append a custom type label to any supported data type to create custom data types\. This capability is similar to *type traits* in programming languages\. For example, if you have an application that needs to know which type of number is being sent in the message, you can create custom types similar to the following: `Number.byte`, `Number.short`, `Number.int`, and `Number.float`\. Another example using the binary data type is to use `Binary.gif` and `Binary.png` to distinguish among different image file types in a message or batch of messages\. The appended data is optional and opaque to Amazon SQS, which means that the appended data isn't interpreted, validated, or used by Amazon SQS\. The Custom Type extension has the same restrictions on allowed characters as the message body\.

## Using Message Attributes with the AWS Management Console<a name="sqs-attrib"></a>

You can use the AWS Management Console to configure message attributes\. In the Amazon SQS console, select a queue, choose the **Queue Actions** drop\-down list, and then select **Send a Message**\. The console expects the user to input a Base\-64\-encoded value for sending a Binary type\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-msg-attrib.png)

On the **Message Attributes** tab, enter a name, select the type, and enter a value for the message attribute\. Optionally, you can also append custom information to the type\. For example, the following screen shows the *Number* type selected with *byte* added for customization\. For more information about custom data for the supported data types, see the [Message Attribute Data Types and Validation](#message-attributes-data-types-validation) section\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-msg-attrib-num-byte.png)

To add an attribute, choose **Add Attribute**\. The attribute information appears in the **Name**, **Type**, and **Values** list\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-msg-attrib-add-attrib.png)

You can also use the console to view information about the message attributes for received messages\. In the console, select a queue, and from the **Queue Actions** drop\-down list select **View/Delete Messages**\. In the list of messages, choose **Message Details** to view the information\. For example, you can see the message attribute size and MD5 message digest\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-msg-attrib-view-md5.png)

## Using Message Attributes with the AWS SDKs<a name="sqs-attrib-java-sdk"></a>

The [AWS SDKs](https://aws.amazon.com/tools/) provide APIs in several languages for using message attributes with Amazon SQS\. This section includes some Java examples that show how to work with message attributes\. These examples can be integrated with the `SimpleQueueServiceSample.java` sample from the SDK for Java\. MessageBody and MessageAttributes checksums are automatically calculated and compared with the data Amazon SQS returns by the latest SDK for Java\. For more information about the SDK for Java, see [Getting Started with the AWS SDK for Java](https://aws.amazon.com/developers/getting-started/java/)\. 

The following three Java examples show how to use the [MessageAttributeValue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_MessageAttributeValue.html) data type to set the `String`, `Number`, and `Binary` parameters for the message attributes: 

*String*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("attributeName", new MessageAttributeValue().withDataType("String").withStringValue("string-value-attribute-value"));
```

*Number*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("attributeName", new MessageAttributeValue().withDataType("Number").withStringValue("230.000000000000000001"));
```

*Binary*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("attributeName", new MessageAttributeValue().withDataType("Binary").withBinaryValue(ByteBuffer.wrap(new byte[10])));
```

The following three examples show how to use the optional custom type for the message attributes:

*String—Custom*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("AccountId", new MessageAttributeValue().withDataType("String.AccountId").withStringValue("000123456"));
```

*Number—Custom*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("AccountId", new MessageAttributeValue().withDataType("Number.AccountId").withStringValue("000123456"));
```

**Note**  
Because the `Type` is a number, the result in the `ReceiveMessage` call is `123456`\.

*Binary—Custom*

```
Map<String, MessageAttributeValue> messageAttributes = new HashMap<>();
messageAttributes.put("PhoneIcon", new MessageAttributeValue().withDataType("Binary.JPEG").withBinaryValue(ByteBuffer.wrap(new byte[10])));
```

To send a message using one of the previous message attribute examples, your code should look similar to the following:

```
SendMessageRequest request = new SendMessageRequest();
request.withMessageBody("A test message body.");
request.withQueueUrl("MyQueueUrlStringHere");
request.withMessageAttributes(messageAttributes);
sqs.sendMessage(request);
```

## Using Message Attributes with the Amazon SQS Query API<a name="sqs-attrib-api"></a>

To specify message attributes with the Query API, you call the [SendMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html), [SendMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html), or [ReceiveMessage](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) actions\.

**Note**  
The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

A Query API request for this example looks similar to the following:

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue
...
?Action=SendMessage
&MessageBody=This+is+a+test+message
&MessageAttribute.1.Name=test_attribute_name_1
&MessageAttribute.1.Value.StringValue=test_attribute_value_1
&MessageAttribute.1.Value.DataType=String
&MessageAttribute.2.Name=test_attribute_name_2
&MessageAttribute.2.Value.StringValue=test_attribute_value_2
&MessageAttribute.2.Value.DataType=String
&Expires=2014-05-05T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Queue names and queue URLs are case\-sensitive\.

The Query API response should look similar to the following:

```
HTTP/1.1 200 OK
...
<SendMessageResponse>
    <SendMessageResult>
        <MD5OfMessageBody>
            fafb00f5732ab283681e124bf8747ed1
        </MD5OfMessageBody>
        <MD5OfMessageAttributes>
        3ae8f24a165a8cedc005670c81a27295
        </MD5OfMessageAttributes>
        <MessageId>
            5fea7756-0ea4-451a-a703-a558b933e274
        </MessageId>
    </SendMessageResult>
    <ResponseMetadata>
        <RequestId>
            27daac76-34dd-47df-bd01-1f6e873584a0
        </RequestId>
    </ResponseMetadata>
</SendMessageResponse>
```

When using `[SendMessageBatch](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)`, the message attributes need to be specified on each individual message in the batch\.

A Query API request for this example looks similar to the following:

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue
...
?Action=SendMessageBatch
&SendMessageBatchRequestEntry.1.Id=test_msg_001
&SendMessageBatchRequestEntry.1.MessageBody=test%20message%20body%201
&SendMessageBatchRequestEntry.2.Id=test_msg_002
&SendMessageBatchRequestEntry.2.MessageBody=test%20message%20body%202
&SendMessageBatchRequestEntry.2.DelaySeconds=60
&SendMessageBatchRequestEntry.2.MessageAttribute.1.Name=test_attribute_name_1
&SendMessageBatchRequestEntry.2.MessageAttribute.1.Value.StringValue=test_attribute_value_1
&SendMessageBatchRequestEntry.2.MessageAttribute.1.Value.DataType=String
&Expires=2014-05-05T22%3A52%3A43PST
&Version=2012-11-05
&AUTHPARAMS
```

The Query API response should look similar to the following:

```
HTTP/1.1 200 OK
...
<SendMessageBatchResponse>
<SendMessageBatchResult>
    <SendMessageBatchResultEntry>
        <Id>test_msg_001</Id>
        <MessageId>0a5231c7-8bff-4955-be2e-8dc7c50a25fa</MessageId>
        <MD5OfMessageBody>0e024d309850c78cba5eabbeff7cae71</MD5OfMessageBody>
    </SendMessageBatchResultEntry>
    <SendMessageBatchResultEntry>
        <Id>test_msg_002</Id>
        <MessageId>15ee1ed3-87e7-40c1-bdaa-2e49968ea7e9</MessageId>
        <MD5OfMessageBody>7fb8146a82f95e0af155278f406862c2</MD5OfMessageBody>
        <MD5OfMessageAttributes>295c5fa15a51aae6884d1d7c1d99ca50</MD5OfMessageAttributes>
    </SendMessageBatchResultEntry>
</SendMessageBatchResult>
<ResponseMetadata>
    <RequestId>ca1ad5d0-8271-408b-8d0f-1351bf547e74</RequestId>
</ResponseMetadata>
</SendMessageBatchResponse>
```

## MD5 Message\-Digest Calculation<a name="sqs-attrib-md5"></a>

If you want to calculate the MD5 me ssage digest for Amazon SQS message attributes and you're either using the Query API or one of the AWS SDKs that doesn't support MD5 message digest for Amazon SQS message attributes, then you must use the following information about the algorithm to calculate the MD5 message digest of the message attributes\. 

**Note**  
Currently the AWS SDK for Java supports MD5 message digest for Amazon SQS message attributes\. This is available in the `MessageMD5ChecksumHandler` class\. If you're using the SDK for Java, then you don't need to use the following information\.

The high\-level steps of the algorithm to calculate the MD5 message digest for Amazon SQS message attributes are:

1. Sort all message attributes by name in ascending order\.

1. Encode the individual parts of each attribute \(name, type, and value\) into a buffer\.

1. Compute the message digest of the entire buffer\.

**To encode a single Amazon SQS message attribute:**

1. Encode the name \(length of name \[4 bytes\] \+ UTF\-8 bytes of the name\)\.

1. Encode the type \(length of type \[4 bytes\] \+ UTF\-8 bytes of the type\)\.

1. Encode the transport type \(string or binary\) of the value \[1 byte\]\.

   1. For the *string* transport type, encode 1\.

   1. For the *binary* transport type, encode 2\.
**Note**  
The string and number logical data types use the *string* transport type\. The binary logical data type uses the *binary* transport type\.

1. Encode the attribute value\.

   1. For a *string* transport type, encode the attribute value \(length \[4 bytes\] \+ the UTF\-8 bytes of the value\)\.

   1. For a *binary* transport type, encode the attribute value \(length \[4 bytes\] \+ use the raw bytes directly\)\.

The following diagram shows the encoding of the MD5 message digest for a single message attribute:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-msg-attrib-md5.png)