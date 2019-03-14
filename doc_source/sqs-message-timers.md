# Amazon SQS Message Timers<a name="sqs-message-timers"></a>

Message timers let you specify an initial invisibility period for a message added to a queue\. For example, if you send a message with a 45\-second timer, the message isn't visible to consumers for its first 45 seconds in the queue\. The default \(minimum\) invisibility period for a message is 0 seconds\. The maximum is 15 minutes\. For information about sending messages with timers using the AWS Management Console or the AWS SDK for Java \(and the `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` action\), see [Tutorial: Sending a Message with a Timer to an Amazon SQS Queue](sqs-send-message-with-timer.md)\.

**Note**  
FIFO queues don't support timers on individual messages\.

To set a delay period on *an entire queue*, rather than on individual messages, use [delay queues](sqs-delay-queues.md)\. A message timer setting for an individual message overrides any `DelaySeconds` value on an Amazon SQS delay queue\. 