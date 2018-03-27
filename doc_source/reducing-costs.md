# Reducing Amazon SQS Costs<a name="reducing-costs"></a>


+ [Batching Message Actions](#batching-message-actions)
+ [Using the Appropriate Polling Mode](#using-appropriate-polling-mode)

## Batching Message Actions<a name="batching-message-actions"></a>

To reduce costs, batch your message actions:

+ To send, receive, and delete messages, and to change the message visibility timeout for multiple messages with a single action, use the [Amazon SQS batch API actions](sqs-batch-api-actions.md)\.

+ To combine client\-side buffering with request batching, use long polling together with the [ buffered asynchronous client](sqs-batch-api-actions.md#sqs-client-side-buffering-request-batching) included with the AWS SDK for Java\.
**Note**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

## Using the Appropriate Polling Mode<a name="using-appropriate-polling-mode"></a>

To take advantage of additional potential reduced cost or near\-instantaneous response, use one of the following polling modes:

+ Long polling lets you consume messages from your Amazon SQS queue as soon as they become available\. 

  + To reduce the cost of using Amazon SQS and to decrease the number of empty receives to an empty queue \(responses to the `ReceiveMessage` action which return no messages\), enable long polling\. For more information, see [Amazon SQS Long Polling](sqs-long-polling.md)\.

  + To increase efficiency when polling for multiple threads with multiple receives, decrease the number of threads\.

  + Long polling is preferable over short polling in most cases\.

+ Short polling returns responses immediately, even if the polled Amazon SQS queue is empty\. 

  + To satisfy the requirements of an application that expects immediate responses to the `ReceiveMessage` request, use short polling\.

  + Short polling is billed the same as long polling\.