# Purging messages from an Amazon SQS queue \(console\)<a name="sqs-using-purge-queue"></a>

If you don't want to delete an Amazon SQS queue but need to delete all of the messages from it, purge the queue\. The message deletion process takes up to 60 seconds\. We recommend waiting for 60 seconds regardless of your queue's size\. 

**Important**  
When you purge a queue, you can't retrieve any of the deleted messages\.

**To purge a queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose the queue to purge\. 

1. Choose **Purge**\.

1. In the **Purge queue** dialog box, confirm the purge by entering **purge** and choosing **Purge**\.

   All messages are purged from the queue\. The console displays a confirmation banner\.