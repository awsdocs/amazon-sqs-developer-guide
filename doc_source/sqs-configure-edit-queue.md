# Editing an Amazon SQS queue \(console\)<a name="sqs-configure-edit-queue"></a>

You can use the Amazon SQS console to edit any queue configuration parameters \(except the queue type\) and add or remove queue features\.

**To edit an Amazon SQS queue \(console\)**

1. Open the [Queues page](https://console.aws.amazon.com/sqs/#/queues) of the Amazon SQS console\.

1. Select a queue, and then choose **Edit**\.

1. \(Optional\) Under **Configuration**, update the queue's [configuration parameters](sqs-configure-queue-parameters.md)\.

1. \(Optional\) To update the [access policy](sqs-configure-add-permissions.md), under **Access policy**, modify the JSON policy\.

1. \(Optional\) To add, update, or remove [encryption](sqs-configure-sse-existing-queue.md), expand **Encryption**\.

1. \(Optional\) To add, update, or remove a [dead\-letter queue](sqs-configure-dead-letter-queue.md) \(which allows you to receive undeliverable messages\), expand **Dead\-letter queue**\.

1. \(Optional\) To add, update, or remove the [tags](sqs-configure-tag-queue.md) for the queue, expand **Tags**\.

1. Choose **Save**\.

   The console displays the **Details** page for the queue\.