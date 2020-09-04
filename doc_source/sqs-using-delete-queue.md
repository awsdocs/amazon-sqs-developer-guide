# Deleting an Amazon SQS queue<a name="sqs-using-delete-queue"></a>

If you no longer use an Amazon SQS queue and don't foresee using it in the near future, we recommend deleting it\. 

**Tip**  
If you want to verify that a queue is empty before you delete it, see [Confirming that a queue is empty](confirm-queue-is-empty.md)\.

You can delete a queue even when it isn't empty\. To delete the messages in a queue but not the queue itself, [purge the queue](sqs-using-purge-queue.md)\.

**To delete a queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. On the **Queues** page, choose the queue to delete\. 

1. Choose **Delete**\.

1. In the **Delete queue** dialog box, confirm the deletion by entering **delete**\.

1. Choose **Delete**\.

**To delete a queue \(AWS CLI AWS API\)**  
You can use one of the following commands to delete a queue:
+ AWS CLI: [aws sqs delete\-queue](https://docs.aws.amazon.com/cli/latest/reference/sqs/delete-queue.html)
+ AWS API: [DeleteQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html)