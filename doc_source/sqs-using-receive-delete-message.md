# Receiving and deleting messages \(console\)<a name="sqs-using-receive-delete-message"></a>

After you send messages to a queue, you can receive and delete them\. When you request messages from a queue, you can't specify which messages to retrieve\. Instead, you specify the maximum number of messages \(up to 10\) that you want to retrieve\.

**Note**  
Because Amazon SQS is a distributed system, a queue with very few messages might display an empty response to a receive request\. In this case, rerun the request to get your message\. Depending on your application's needs, you might have to use [short or long polling](sqs-short-and-long-polling.md) to receive messages\.

Amazon SQS doesn't automatically delete a message after retrieving it for you, in case you don't successfully receive the message \(for example, if the consumers fail or you lose connectivity\)\. To delete a message, you must send a separate request which acknowledges that you've successfully received and processed the message\. Note that you must receive a message before you can delete it\. 

**To receive and delete a message \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose a queue\.

1. From **Actions**, choose **Send and receive messages**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-send-a-message.png)

   The console displays the **Send and receive messages** page\.

1. Choose **Poll for messages**\.

   Amazon SQS begins to poll for messages in the queue\. The progress bar on the right side of the **Receive messages** section displays the duration of polling\.

   The **Messages** section displays a list of the received messages\. For each message, the list displays the message ID, Sent date, Size, and Receive count\.

1. To delete messages, choose the messages that you want to delete and choose **Delete**\.

1. In the **Delete Messages** dialog box, choose **Delete**\.