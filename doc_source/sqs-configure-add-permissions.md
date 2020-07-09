# Configuring access policy \(console\)<a name="sqs-configure-add-permissions"></a>

When you [edit](sqs-configure-edit-queue.md) a queue, you can configure its access policy\.

The access policy defines the accounts, users, and roles that can access the queue\. The access policy also defines the actions \(such as `SendMessage`, `ReceiveMessage`, or `DeleteMessage`\) that the users can access\. The default policy allows only the queue owner to send and receive messages\. 

**To configure the access policy for an existing queue \(console\)**

1. Open the Amazon SQS console at [https://console\.aws\.amazon\.com/sqs/](https://console.aws.amazon.com/sqs/)\.

1. In the navigation pane, choose **Queues**\. 

1. Choose a queue and choose **Edit**\. 

1. Scroll to the **Access policy** section\.

1. Edit the access policy statements in the input box\. 

1. When you finish configuring the access policy, choose **Save**\.