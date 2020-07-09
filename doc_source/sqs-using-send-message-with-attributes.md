# Sending a message with attributes \(console\)<a name="sqs-using-send-message-with-attributes"></a>

For standard and FIFO queues, you can include structured metadata \(such as timestamps, geospatial data, signatures, and identifiers\) with messages\. For more information, see [Amazon SQS message attributes](sqs-message-metadata.md#sqs-message-attributes)\.

**To send a message with attributes to a queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose a queue\.

1. From **Actions**, choose **Send and receive messages**\. 

1. Enter the message attribute parameters\. 

   1. In the name text box, enter a unique name of up to 256 characters\. 

   1. For the attribute type, choose **String**, **Number**, or **Binary**\.

   1. \(Optional\) Enter a custom data type\. For example, you could add **byte**, **int**, or **float** as custom data types for **Number**\. 

   1. In the value text box, enter the message attribute value\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes.png)

1. To add another message attribute\., choose **Add new attribute**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-with-attributes-custom-attribute.png)

1. You can modify the attribute values any time before sending the message\. 

1. To delete an attribute, choose **Remove**\. To delete the first attribute, close **Message attributes**\.

1. When you finish adding attributes to the message, choose **Send message**\. Your message is sent and the console displays a success message\. To view information about the message attributes of the sent message, choose **View details**\. Choose **Done** to close the **Message details** dialog box\.