# Tutorial: Adding Permissions to an Amazon SQS Queue<a name="sqs-add-permissions"></a>

You can specify to whom you allow \(or explicitly deny\) the ability to interact with your queue in specific ways by adding permissions to a queue\. The following example demonstrates adding the permission for anyone to get a queue's URL\.

**Note**  
An Amazon SQS policy can have a maximum of 7 actions\.

## AWS Management Console<a name="add-permissions-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. From **Queue Actions**, select **Add a Permission**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-adding-permissions-add-a-permission.png)

   The **Add a Permission** dialog box is displayed\.

1. In this example, you allow anyone to get the queue's URL:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-adding-permissions-add-a-permission-dialog-box.png)

   1. Ensure that next to **Effect**, **Allow** is selected\.

   1. Next to **Principal**, check the **Everybody** box\.

   1. From the **Actions** drop\-down list, select **GetQueueUrl** box\.

   1. Choose **Add Permission**\.

   The permission is added to the queue\.

   Your queues's policy **Effect**, **Principals**, **Actions**, and **Conditions** are displayed on your queue's **Permissions** tab\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-adding-permissions-permissions-tab.png)