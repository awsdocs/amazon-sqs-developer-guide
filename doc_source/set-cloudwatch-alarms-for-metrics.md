# Setting CloudWatch Alarms for Amazon SQS Metrics<a name="set-cloudwatch-alarms-for-metrics"></a>

CloudWatch allows you to trigger alarms when a threshold is met for a metric\. For example, you can set an alarm for the `NumberOfMessagesSent` metric so that when the number of messages exceeds a specified limit over a specified time period, then an email notification can be sent to inform you of the event\.

**Amazon CloudWatch Console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  In the navigation pane, choose **Alarms**, and then choose **Create Alarm**\. The **Create Alarm** dialog box displays\. 

1.  On the **Select Metric** page, choose **Browse Metrics**, **SQS**:   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-alarm-dialog.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. For **SQS > Queue Metrics**, choose \(check\) the box that you want to set an alarm for the combination of **QueueName** and **Metric Name**\. \(For a list of available metrics, see [Available CloudWatch Metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)\)\. For example, choosing \(checking\) the box for **MyQueue**, **NumberOfMessagesSent** sets an alarm based on the number of messages sent to the `MyQueue` queue\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-alarm-select-metric.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. Choose **Next**\. The **Define Alarm** page displays\.

1. For **Alarm Threshold**, fill in the **Name** and **Description** boxes\. For **is**, **for**, **Period**, and **Statistic**, specify the conditions for the alarm\. For example, let's say you chose \(checked\) the box for **MyQueue**, **NumberOfMessagesSent** on the **Select Metric** page, and you want to alarm when more than 100 messages are sent in any hour to the `MyQueue` queue\. You'd then set the following:

   + Set **is** to **> 100**\.

   + Set **for** to **1**\.

   + Set **Period** to **1 Hour**\.

   + Set **Statistic** to **Sum**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-alarm-define.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

1. For **Actions** and **Whenever this alarm**, choose **State is ALARM**\. For **Send notification to**, if you want CloudWatch to send you an email when the alarm state is reached, either select an existing Amazon SNS topic or choose **New list**\. If you choose **New list**, you can set the name and list comma\-separated email addresses for a new topic\. This list is saved; it appears for future alarms\. 
**Note**  
If you choose **New list** to create a new Amazon SNS topic, the email addresses must be verified before they receive any notifications\. Emails are sent only when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, they won't receive a notification\. 

1. Choose **Create Alarm**\. CloudWatch creates the alarm and then displays the alarms list\. 

For more information, see [Creating Amazon CloudWatch Alarms](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.