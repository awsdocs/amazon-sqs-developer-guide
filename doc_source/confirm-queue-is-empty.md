# Confirming that a queue is empty<a name="confirm-queue-is-empty"></a>

In most cases, you can use [long polling](sqs-short-and-long-polling.md#sqs-long-polling) to determine if a queue is empty\. In rare cases, you might receive empty responses even when a queue still contains messages, especially if you specified a low value for **Receive message wait time** when you created the queue\. This section describes how to confirm that a queue is empty\.

**To confirm that a queue is empty \(console\)**

1. Stop all producers from sending messages\.

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose a queue\. 

1. Choose the **Monitoring** tab\.

1. At the top right of the Monitoring dashboards, choose the down arrow next to the Refresh symbol\. From the dropdown menu, choose **Auto refresh**\. Leave the **Refresh interval** at **1 Minute**\.

1. Observe the following dashboards: 
   + Approximate Number Of Messages Delayed
   + Approximate Number Of Messages Not Visible
   + Approximate Number Of Messages Visible

   When all of them show `0` values for several minutes, the queue is empty\.

**To confirm that a queue is empty \(AWS CLI, AWS API\)**

1. Stop all producers from sending messages\. 

1. Repeatedly run one of the following commands:
   + AWS CLI: `[get\-queue\-attributes](https://docs.aws.amazon.com/cli/latest/reference/get-queue-attributes.html)`
   + AWS API: `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)`

1. Observe the metrics for the following attributes:
   + `ApproximateNumberOfMessagesDelayed`
   + `ApproximateNumberOfMessagesNotVisible`
   + `ApproximateNumberOfMessagesVisible`

   When all of them are `0` for several minutes, the queue is empty\.

If you rely on Amazon CloudWatch metrics, make sure that you see multiple consecutive zero data points before considering that queue empty\. For more information on CloudWatch metrics, see [Available CloudWatch metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)\.