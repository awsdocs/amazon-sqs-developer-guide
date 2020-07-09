# Configuring Amazon SQS queues \(console\)<a name="sqs-configuring"></a>

Use the Amazon SQS console to configure and manage Amazon Simple Queue Service \(Amazon SQS\) queues and features\. You can also use the console to configure features such as server\-side encryption, associate a dead\-letter queue with your queue, or set a trigger to invoke an AWS Lambda function\. 

## Understanding the Amazon SQS console<a name="sqs-configure-overview"></a>

When you open the console, choose **Queues** from the navigation pane to display the **Queues** page\. The **Queues** page provides information about all of your queues in the active region\. 

![\[Queues page in the Amazon SQS console\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-config-queue-list.png)

The entry for each queue shows the queue type and other information about the queue\. The **Type** column helps you distinguish standard queues from First\-In\-First Out \(FIFO\) queues at a glance\.

From the **Queues** page, there are two ways to perform actions on a queue\. You can choose the option next to the queue name and then choose the action you want to perform on the queue\.

![\[Actions available in the Queues page of the Amazon SQS console\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/queues-page-actions.png)

You can also choose the queue name, which open the **Details** page for the queue\. The **Details** page includes the same actions as the **Queues** page\. In addition, you can choose one of the tabs below the **Details** section to view additional configuration details and actions\. 

![\[Queue details page in the Amazon SQS console\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/queue-details-page.png)