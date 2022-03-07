# Configuring server\-side encryption \(SSE\) for a queue using SQS\-owned encryption keys \(console\)<a name="sqs-configure-sqs-sse-queue"></a>

Amazon SQS managed SSE \(SSE\-SQS\) is managed server\-side encryption that uses SQS\-owned encryption keys to protect sensitive data sent over message queues\. With SSE\-SQS, you don't need to create and manage encryption keys, or modify your code to encrypt your data\. SSE\-SQS lets you transmit data securely and helps you meet strict encryption compliance and regulatory requirements at no additional cost\.

SSE\-SQS protects data at rest using 256\-bit Advanced Encryption Standard \(AES\-256\) encryption\. SSE encrypts messages as soon as Amazon SQS receives them\. Amazon SQS stores messages in encrypted form and decrypts them only when sending them to an authorized consumer\.

**To configure SSE\-SQS encryption for a queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\.

1. Choose a queue, and then choose **Edit**\.

1. Expand **Encryption**\.

1. For **Server\-side encryption**, choose **Enabled**\.

1. Select **Amazon SQS key \(SSE\-SQS\)**\. There is no additional fee for using this option\.

1. Choose **Save**\.