# Accessing CloudWatch metrics for Amazon SQS<a name="sqs-access-metrics"></a>

Amazon SQS and Amazon CloudWatch are integrated so you can use CloudWatch to view and analyze metrics for your Amazon SQS queues\. You can view and analyze your queues' metrics from the [Amazon SQS console](#access-cloudwatch-metrics-sqs-console), the [CloudWatch console](#access-metrics-cloudwatch-console), using the [AWS CLI](#access-cloudwatch-metrics-cli), or using the [CloudWatch API](#access-metrics-cloudwatch-api)\. You can also [set CloudWatch alarms](set-cloudwatch-alarms-for-metrics.md) for Amazon SQS metrics\.

## Amazon SQS console<a name="access-cloudwatch-metrics-sqs-console"></a>

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. In the list of queues, choose \(check\) the boxes for the queues that you want to access metrics for\. You can show metrics for up to 10 queues\.

1. Choose the **Monitoring** tab\.

   Various graphs are displayed in the **SQS metrics** section\.

1. To understand what a particular graph represents, hover over ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/information.png) next to the desired graph, or see [Available CloudWatch metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)\.

1. To change the time range for all of the graphs at the same time, for **Time Range**, choose the desired time range \(for example, **Last Hour**\)\. 

1. To view additional statistics for an individual graph, choose the graph\.

1. In the **CloudWatch Monitoring Details** dialog box, select a **Statistic**, \(for example, **Sum**\)\. For a list of supported statistics, see [Available CloudWatch metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)\.

1. To change the time range and time interval that an individual graph displays \(for example, to show a time range of the last 24 hours instead of the last 5 minutes, or to show a time period of every hour instead of every 5 minutes\), with the graph's dialog box still displayed, for **Time Range**, choose the desired time range \(for example, **Last 24 Hours**\)\. For **Period**, choose the desired time period within the specified time range \(for example, **1 Hour**\)\. When you're finished looking at the graph, choose **Close**\.

1. \(Optional\) To work with additional CloudWatch features, on the **Monitoring** tab, choose **View all CloudWatch metrics**, and then follow the instructions in the [Amazon CloudWatch console](#access-metrics-cloudwatch-console) procedure\.

## Amazon CloudWatch console<a name="access-metrics-cloudwatch-console"></a>

1. Sign in to the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation panel, choose **Metrics**\.

1. Select the **SQS** metric namespace\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-queue-metrics-namespace.png)

1. Select the **Queue Metrics** metric dimension\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-queue-metrics-dimension.png)

1. You can now examine your Amazon SQS metrics:
   + To sort the metrics, use the column heading\.
   + To graph a metric, select the check box next to the metric\.
   + To filter by metric, choose the metric name and then choose **Add to search**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-cloudwatch-queue-metrics-examine.png)

For more information and additional options, see [Graph Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/graph_metrics.html) and [Using Amazon CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) in the *Amazon CloudWatch User Guide*\.

## AWS Command Line Interface<a name="access-cloudwatch-metrics-cli"></a>

To access Amazon SQS metrics using the AWS CLI, run the `[get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html)` command\.

For more information, see [Get Statistics for a Metric](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/getting-metric-statistics.html) in the *Amazon CloudWatch User Guide*\.

## CloudWatch API<a name="access-metrics-cloudwatch-api"></a>

To access Amazon SQS metrics using the CloudWatch API, use the `[GetMetricStatistics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html)` action\.

For more information, see [Get Statistics for a Metric](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/getting-metric-statistics.html) in the *Amazon CloudWatch User Guide*\.