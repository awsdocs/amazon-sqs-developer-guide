# Creating CloudWatch alarms for Amazon SQS metrics<a name="set-cloudwatch-alarms-for-metrics"></a>

CloudWatch lets you trigger alarms based on a metric threshold\. For example, you can create an alarm for the `NumberOfMessagesSent` metric\. For example, if more than 100 messages are sent to the `MyQueue` queue in 1 hour, an email notification is sent out\. For more information, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.<a name="set-alarm-cloudwatch-console"></a>

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Alarms**, and then choose **Create Alarm**\.

1. In the **Select Metric** section of the **Create Alarm** dialog box, choose **Browse Metrics**, **SQS**\.

1. For **SQS > Queue Metrics**, choose the **QueueName** and **Metric Name** for which to set an alarm, and then choose **Next**\. For a list of available metrics, see [Available CloudWatch metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)\.

   In the following example, the selection is for an alarm for the `NumberOfMessagesSent` metric for the `MyQueue` queue\. The alarm triggers when the number of sent messages exceeds 100\.

1. In the **Define Alarm** section of the **Create Alarm** dialog box, do the following:

   1. Under **Alarm Threshold**, type the **Name** and **Description** for the alarm\.

   1. Set **is** to **> 100**\.

   1. Set **for** to **1 out of 1 datapoints**\.

   1. Under **Alarm preview**, set **Period** to **1 Hour**\.

   1. Set **Statistic** to **Standard**, **Sum**\.

   1. Under **Actions**, set **Whenever this alarm** to **State is ALARM**\.

      If you want CloudWatch to send a notification when the alarm is triggered, select an existing Amazon SNS topic or choose **New list** and enter email addresses separated by commas\.
**Note**  
If you create a new Amazon SNS topic, the email addresses must be verified before they receive any notifications\. If the alarm state changes before the email addresses are verified, the notifications aren't delivered\. 

1. Choose **Create Alarm**\. 

   The alarm is created\.