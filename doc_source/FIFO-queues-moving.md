# Moving from a standard queue to a FIFO queue<a name="FIFO-queues-moving"></a>

If you have an existing application that uses standard queues and you want to take advantage of the ordering or exactly\-once processing features of FIFO queues, you need to configure the queue and your application correctly\.

**Note**  
You can't convert an existing standard queue into a FIFO queue\. To make the move, you must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue\.

To make sure that your application correctly works with a FIFO queue, use the following checklist:
+ If you use [batching](sqs-batch-api-actions.md), FIFO queues support up to 3,000 messages per second, per API method \(`SendMessageBatch`, `ReceiveMessage`, or `DeleteMessageBatch`\)\. The 3000 messages per second represent 300 API calls, each with a batch of 10 messages\. To request a quota increase, [submit a support request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-sqs)\. Without batching, FIFO queues support up to 300 API calls per second, per API method \(`SendMessage`, `ReceiveMessage`, or `DeleteMessage`\)\. 
+ FIFO queues don't support per\-message delays, only per\-queue delays\. If your application sets the same value of the `DelaySeconds` parameter on each message, you must modify your application to remove the per\-message delay and set `DelaySeconds` on the entire queue instead\.
+ Every message sent to a FIFO queue requires a message group ID\. If you don't need multiple ordered message groups, specify the same message group ID for all your messages\.
+ Before sending messages to a FIFO queue, confirm the following:
  + If your application can send messages with identical message bodies, you can modify your application to provide a unique message deduplication ID for each sent message\.
  + If your application sends messages with unique message bodies, you can enable content\-based deduplication\.
+ You don't have to make any code changes to your consumer\. However, if it takes a long time to process messages and your visibility timeout is set to a high value, consider adding a receive request attempt ID to each `ReceiveMessage` action\. This allows you to retry receive attempts in case of networking failures and prevents queues from pausing due to failed receive attempts\.

For more information, see the * [Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.