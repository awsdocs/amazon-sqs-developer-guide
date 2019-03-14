# Basic Amazon SQS Policy Examples<a name="sqs-writing-an-sqs-policy"></a>

The following examples provide an introduction to Amazon SQS permission policies\.

## Example 1: Allow a User to Create Queues<a name="allow-queue-creation"></a>

In the following example, we create a policy for Bob that lets him access all Amazon SQS actions, but only with queues whose names are prefixed with the literal string `bob_queue_`\.

Amazon SQS doesn't automatically grant the creator of a queue permissions to use the queue\. Therefore, we must explicitly grant Bob permissions to use all Amazon SQS actions in addition to `CreateQueue` action in the IAM policy\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:*:123456789012:bob_queue_*"
   }]
}
```

## Example 2: Allow Developers to Write Messages to a Shared Queue<a name="write-messages-to-shared-queue"></a>

In the following example, we create a group for developers and attach a policy that lets the group use the Amazon SQS `SendMessage` action, but only with the queue that belongs to the specified AWS account and is named `MyCompanyQueue`\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "sqs:SendMessage",
      "Resource": "arn:aws:sqs:*:123456789012:MyCompanyQueue"
   }]
}
```

You can use `*` instead of `SendMessage` to grant the following actions to a principal on a shared queue: `ChangeMessageVisibility`, `DeleteMessage`, `GetQueueAttributes`, `GetQueueUrl`, `ReceiveMessage`, and `SendMessage`\.

**Note**  
Although `*` includes access provided by other permission types, Amazon SQS considers permissions separately\. For example, it is possible to grant both `*` and `SendMessage` permissions to a user, even though a `*` includes the access provided by `SendMessage`\.  
This concept also applies when you remove a permission\. If a principal has only a `*` permission, requesting to remove a `SendMessage` permission *doesn't* leave the principal with an *everything\-but* permission\. Instead, the request has no effect, because the principal doesn't possess an explicit `SendMessage` permission\. To leave the principal with only the `ReceiveMessage` permission, first add the `ReceiveMessage` permission and then remove the `*` permission\.

## Example 3: Allow Managers to Get the General Size of Queues<a name="get-size-of-queues"></a>

In the following example, we create a group for managers and attach a policy that lets the group use the Amazon SQS `GetQueueAttributes` action with all of the queues that belong to the specified AWS account\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "sqs:GetQueueAttributes",
      "Resource": "*"   
   }]
}
```

## Example 4: Allow a Partner to Send Messages to a Specific Queue<a name="send-messages-to-specific-queue"></a>

You can accomplish this task using an Amazon SQS policy or an IAM policy\. If your partner has an AWS account, it might be easier to use an Amazon SQS policy\. However, any user in the partner's company who possesses the AWS security credentials can send messages to the queue\. If you want to limit access to a particular user or application, you must treat the partner like a user in your own company and use an IAM policy instead of an Amazon SQS policy\.

This example performs the following actions:

1. Create a group called WidgetCo to represent the partner company\.

1. Create a user for the specific user or application at the partner's company who needs access\.

1. Add the user to the group\.

1. Attach a policy that gives the group access only to the `SendMessage` action for only the queue named `WidgetPartnerQueue`\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
         "Effect": "Allow",
         "Action": "sqs:SendMessage",
         "Resource": "arn:aws:sqs:*:123456789012:WidgetPartnerQueue"
   }]
}
```
