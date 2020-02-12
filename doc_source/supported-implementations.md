# Supported JMS 1\.1 Implementations<a name="supported-implementations"></a>

The Amazon SQS Java Messaging Library supports the following [JMS 1\.1 implementations](http://docs.oracle.com/javaee/6/api/javax/jms/package-summary.html)\. For more information about the supported features and capabilities of the Amazon SQS Java Messaging Library, see the [Amazon SQS FAQ](https://aws.amazon.com/sqs/faqs/)\.

## Supported Common Interfaces<a name="supported-common-interfaces"></a>
+ `Connection`
+ `ConnectionFactory`
+ `Destination`
+ `Session`
+ `MessageConsumer`
+ `MessageProducer`

## Supported Message Types<a name="supported-message-types"></a>
+ `ByteMessage`
+ `ObjectMessage`
+ `TextMessage`

## Supported Message Acknowledgment Modes<a name="supported-message-acknowledgement-modes"></a>
+ `AUTO_ACKNOWLEDGE`
+ `CLIENT_ACKNOWLEDGE`
+ `DUPS_OK_ACKNOWLEDGE`
+ `UNORDERED_ACKNOWLEDGE`

**Note**  
The `UNORDERED_ACKNOWLEDGE` mode isn't part of the JMS 1\.1 specification\. This mode helps Amazon SQS allow a JMS client to explicitly acknowledge a message\.

## JMS\-Defined Headers and Reserved Properties<a name="jms-defined-headers-reserved-properties"></a>

### For Sending Messages<a name="for-sending-messages"></a>

When you send messages, you can set the following headers and properties for each message:
+ `JMSXGroupID` \(required for FIFO queues, not allowed for standard queues\)
+ `JMS_SQS_DeduplicationId` \(optional for FIFO queues, not allowed for standard queues\)

After you send messages, Amazon SQS sets the following headers and properties for each message:
+ `JMSMessageID`
+ `JMS_SQS_SequenceNumber` \(only for FIFO queues\)

### For Receiving Messages<a name="for-receiving-messages"></a>

When you receive messages, Amazon SQS sets the following headers and properties for each message:
+ `JMSDestination`
+ `JMSMessageID`
+ `JMSRedelivered`
+ `JMSXDeliveryCount`
+ `JMSXGroupID` \(only for FIFO queues\)
+ `JMS_SQS_DeduplicationId` \(only for FIFO queues\)
+ `JMS_SQS_SequenceNumber` \(only for FIFO queues\)