# Monitoring Amazon SQS using CloudWatch<a name="sqs-monitoring-using-cloudwatch"></a>

Amazon SQS and Amazon CloudWatch are integrated so you can use CloudWatch to view and analyze metrics for your Amazon SQS queues\. You can view and analyze your queues' metrics from the Amazon SQS console, the CloudWatch console, using the AWS CLI, or using the CloudWatch API\. You can also set CloudWatch alarms for Amazon SQS metrics\.

CloudWatch metrics for your Amazon SQS queues are automatically collected and pushed to CloudWatch every five minutes\. These metrics are gathered on all queues that meet the CloudWatch guidelines for being *active*\. CloudWatch considers a queue to be active for up to six hours if it contains any messages or if any API action accesses it\. 

**Note**  
There is no charge for the Amazon SQS metrics reported in CloudWatch\. They're provided as part of the Amazon SQS service\.  
Detailed monitoring \(or one\-minute metrics\) is currently unavailable for Amazon SQS\. Making requests to CloudWatch at this resolution might return no data\.  
CloudWatch metrics are supported for both standard and FIFO queues\.


+ [Access CloudWatch Metrics for Amazon SQS](sqs-access-metrics.md)
+ [Setting CloudWatch Alarms for Amazon SQS Metrics](set-cloudwatch-alarms-for-metrics.md)
+ [Available CloudWatch Metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)