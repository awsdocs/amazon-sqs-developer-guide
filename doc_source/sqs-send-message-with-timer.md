# Tutorial: Sending a Message with a Timer to an Amazon SQS Queue<a name="sqs-send-message-with-timer"></a>

Message timers let you specify an initial invisibility period for a message added to a queue\. For example, if you send a message with a 45\-second timer, the message isn't visible to consumers for its first 45 seconds in the queue\. The default \(minimum\) invisibility period for a message is 0 seconds\. The maximum is 15 minutes\. In this tutorial you'll learn how to send a message with a timer to an existing queue\. For more information, see [Amazon SQS Message Timers](sqs-message-timers.md)\.

**Note**  
FIFO queues don't support timers on individual messages\.

For a more detailed explanation of sending messages to standard and FIFO queues, see [Tutorial: Sending a Message to an Amazon SQS Queue](sqs-send-message.md)\.

**Topics**
+ [AWS Management Console](#send-message-with-timer-console)
+ [AWS SDK for Java](#send-message-with-timer-java)

## AWS Management Console<a name="send-message-with-timer-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue-standard.png)

1. From **Queue Actions**, select **Send a Message**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-standard.png)

   The **Send a Message to *QueueName*** dialog box is displayed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message-dialog-box-standard.png)

1. To send a message to a standard queue, type the **Message Body**, choose **Delay delivery of this message by** and type a value, for example `60` seconds\.

1. Choose **Send Message**\.

   Your message is sent and the **Send a Message to *QueueName*** dialog box is displayed, showing the attributes of the sent message\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes-custom-attribute-message-attributes-standard.png)

1. Choose **Close**\.

## AWS SDK for Java<a name="send-message-with-timer-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To send a message with a timer to a queue<a name="send-message-with-timer-procedure"></a>

1. Copy the [standard queue example program](standard-queues-getting-started-java.md)\.

1. Change the `main` method signature to the following:

   ```
   public static void main(String[] args) throws InterruptedException
   ```

1. Replace the section of the code that sends the message with the following:

   ```
   // Send a message with a 5-second timer.
   System.out.println("Sending a message with a 5-second timer to MyQueue.\n");
   SendMessageRequest request = new SendMessageRequest(myQueueUrl, "This is my message text.");
   request.setDelaySeconds(5);
   sqs.sendMessage(request);
   
   // Wait for 10 seconds.
   System.out.println("Waiting for 10 seconds.");
   Thread.sleep(10000L);
   ```

1. Compile and run the example\.

   The message is sent to the queue\. The response includes the following items:
   + The [message ID](sqs-general-identifiers.md#message-id) Amazon SQS assigns to the message\.
   + An MD5 digest of the message body, used to confirm that Amazon SQS received the message correctly \(for more information, see [RFC1321](http://faqs.org/rfcs/rfc1321.html)\)\.
   + The *request ID* that Amazon SQS assigned to your request\.