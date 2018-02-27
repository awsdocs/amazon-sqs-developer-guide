# Using Identity\-Based Policies \(IAM\) Policies for Amazon SQS<a name="sqs-using-identity-based-policies"></a>

This topic provides examples of identity\-based policies in which an account administrator can attach permissions policies to IAM identities \(users, groups, and roles\)\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your Amazon Simple Queue Service resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon Simple Queue Service Resource](sqs-overview-of-managing-access.md)\.  
With the exception of `ListQueues`, all Amazon SQS API actions support resource\-level permissions\. For more information, see [Amazon SQS API Permissions: Actions and Resource Reference](sqs-api-permissions-reference.md)\.

## Using Amazon SQS and IAM Policies<a name="sqs-using-sqs-and-iam-policies"></a>

There are two ways to give your users permissions to your Amazon SQS resources: using the Amazon SQS policy system and using the IAM policy system\. You can use one or the other, or both\. For the most part, you can achieve the same result with either one\.

For example, the following diagram shows an IAM policy and an Amazon SQS policy equivalent to it\. The IAM policy grants the rights to the Amazon SQS `ReceiveMessage` and `SendMessage` actions for the queue called `queue_xyz` in your AWS Account, and the policy is attached to users named Bob and Susan \(Bob and Susan have the permissions stated in the policy\)\. This Amazon SQS policy also gives Bob and Susan rights to the `ReceiveMessage` and `SendMessage` actions for the same queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-iam-policies-equivalent.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

**Note**  
This example shows simple policies without conditions\. You can specify a particular condition in either policy and get the same result\.

There is one major difference between IAM and Amazon SQS policies: the Amazon SQS policy system lets you grant permission to other AWS Accounts, whereas IAM doesn't\.

It is up to you how you use both of the systems together to manage your permissions\. The following examples show how the two policy systems work together\.

+ In the first example, Bob has both an IAM policy and an Amazon SQS policy that apply to his account\. The IAM policy grants his account permission for the `ReceiveMessage` action on `queue_xyz`, whereas the Amazon SQS policy gives his account permission for the `SendMessage` action on the same queue\. The following diagram illustrates the concept\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-iam-policies-union.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

  If Bob sends a `ReceiveMessage` request to `queue_xyz`, the IAM policy allows the action\. If Bob sends a `SendMessage` request to `queue_xyz`, the Amazon SQS policy allows the action\.

+ In the second example, Bob abuses his access to `queue_xyz`, so it becomes necessary to remove his entire access to the queue\. The easiest thing to do is to add a policy that denies him access to all actions for the queue\. This policy overrides the other two because an explicit `deny` always overrides an `allow`\. For more information about policy evaluation logic, see [Creating Custom Policies Using the Amazon SQS Access Policy Language](sqs-creating-custom-policies.md)\. The following diagram illustrates the concept\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-iam-policies-deny-override.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)

  You can also add an additional statement to the Amazon SQS policy that denies Bob any type of access to the queue\. It has the same effect as adding an IAM policy that denies Bob access to the queue\. For examples of policies that cover Amazon SQS actions and resources, see [Customer\-Managed Policy Examples](#sqs-customer-managed-policy-examples)\. For more information about writing Amazon SQS policies, see [Creating Custom Policies Using the Amazon SQS Access Policy Language](sqs-creating-custom-policies.md)\.

## Permissions Required to Use the Amazon SQS Console<a name="sqs-console-permissions"></a>

A user who wants to work with the Amazon SQS console must have the minimum set of permissions to work with the Amazon SQS queues in the user's AWS account\. For example, the user must have the permission to call the `ListQueues` action to be able to list queues, or the permission to call the `CreateQueue` action to be able to create queues\. In addition to Amazon SQS permissions, to subscribe an Amazon SQS queue to an Amazon SNS topic, the console also requires permissions for Amazon SNS actions\.

If you create an IAM policy that is more restrictive than the minimum required permissions, the console might not function as intended for users with that IAM policy\.

You don't need to allow minimum console permissions for users that make calls only to the AWS CLI or Amazon SQS API actions\. 

## AWS\-Managed \(Predefined\) Policies for Amazon SQS<a name="sqs-access-policy-aws-managed-policies"></a>

AWS addresses many common use cases by providing standalone AWS\-managed IAM policies\. These AWS\-managed policies simplify working with permissions by granting the permissions necessary for common use cases\. For more information, see [AWS Managed Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\. 

The following AWS\-managed policies \(that you can attach to users in your account\) are specific to Amazon SQS:

+ **AmazonSQSReadOnlyAccess** – Grants read\-only access to Amazon SQS queues using the AWS Management Console\.

+ **AmazonSQSFullAccess** – Grants full access to Amazon SQS queues using the AWS Management Console\.

You can search and review available policies on the IAM console\. You can also create your own custom IAM policies to allow permissions for Amazon SQS actions and queues\. You can attach these custom policies to the IAM users or groups that require permissions\. 

## Writing Amazon SQS Policies<a name="sqs-writing-an-sqs-policy"></a>

The following examples provide an introductory breakdown of a permissions policy\.

### Example 1: Allow a User to Create Queues<a name="allow-queue-creation"></a>

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

### Example 2: Allow Developers to Write Messages to a Shared Queue<a name="write-messages-to-shared-queue"></a>

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
This concept also applies when you remove a permission\. If a principal has only a `*` permission, requesting to remove a `SendMessage` permission *doesn't* leave the principal with an *everything\-but* permission\. Instead, the request has no effect, because the principal doesn't possess an explicit `SendMessage` permission\. To leave the principal with onlt the `ReceiveMessage` permission, first add the `ReceiveMessage` permission and then remove the `*` permission\.

### Example 3: Allow Managers to Get the General Size of Queues<a name="get-size-of-queues"></a>

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

### Example 4: Allow a Partner to Send Messages to a Specific Queue<a name="send-messages-to-specific-queue"></a>

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

## Customer\-Managed Policy Examples<a name="sqs-customer-managed-policy-examples"></a>

This section shows example policies for common Amazon SQS use cases\.

You can use the console to verify the effects of each policy as you attach the policy to the user\. Initially, the user doesn't have permissions and won't be able to do anything in the console\. As you attach policies to the user, you can verify that the user can perform various actions in the console\.

**Note**  
We recommend that you use two browser windows: one to grant permissions and the other to sign into the AWS Management Console using the user's credentials to verify permissions as you grant them to the user\.

### Example 1: Grant One Permission to One AWS Account<a name="grant-one-permission-to-one-account"></a>

The following example policy grants AWS account number `111122223333` the `SendMessage` permission for the queue named `444455556666/queue1` in the US East \(Ohio\) region\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_SendMessage",
      "Effect": "Allow",
      "Principal": {
         "AWS": [ 
            "111122223333"
         ]
      },
      "Action": "sqs:SendMessage",
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue1"
   }]  
}
```

### Example 2: Grant Two Permissions to One AWS Account<a name="grant-two-permissions-to-one-account"></a>

The following example policy grants AWS account number `111122223333` both the `SendMessage` and `ReceiveMessage` permission for the queue named `444455556666/queue1`\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_Send_Receive",
      "Effect": "Allow",
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ],
      "Resource": "arn:aws:sqs:*:444455556666:queue1"
   }]
}
```

### Example 3: Grant All Permissions to Two AWS Accounts<a name="grant-all-permissions-to-two-accounts"></a>

The following example policy grants two different AWS accounts numbers \(`111122223333` and `444455556666`\) permission to use all actions to which Amazon SQS allows shared access for the queue named `123456789012/queue1` in the US East \(Ohio\) region\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AllActions",
      "Effect": "Allow",
      "Principal": {
         "AWS": [
            "111122223333",
            "444455556666"
         ]
      },
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:us-east-2:123456789012:queue1"
   }]
}
```

### Example 4: Grant Cross\-Account Permissions to a Role and a User Name<a name="grant-cross-account-permissions-to-role-and-user-name"></a>

The following example policy grants `role1` and `username1` under AWS account number `111122223333` cross\-account permission to use all actions to which Amazon SQS allows shared access for the queue named `123456789012/queue1` in the US East \(Ohio\) region\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AllActions",
      "Effect": "Allow",
      "Principal": {
         "AWS": [
            "arn:aws:iam::111122223333:role/role1",
            "arn:aws:iam::111122223333:user/username1"
         ]
      },
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:us-east-2:123456789012:queue1"
   }]
}
```

### Example 5: Grant a Permission to All Users<a name="grant-permissions-to-all-users"></a>

The following example policy grants all users \(anonymous users\) `ReceiveMessage` permission for the queue named `111122223333/queue1`\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AnonymousAccess_ReceiveMessage",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "sqs:ReceiveMessage",
      "Resource": "arn:aws:sqs:*:111122223333:queue1"
   }]
}
```

### Example 6: Grant a Time\-Limited Permission to All Users<a name="grant-time-limited-permission-to-all-users"></a>

The following example policy grants all users \(anonymous users\) `ReceiveMessage` permission for the queue named `111122223333/queue1`, but only between 12:00 p\.m\. \(noon\) and 3:00 p\.m\. on January 31, 2009\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AnonymousAccess_ReceiveMessage_TimeLimit",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "sqs:ReceiveMessage",
      "Resource": "arn:aws:sqs:*:111122223333:queue1",
      "Condition" : {
         "DateGreaterThan" : {
            "aws:CurrentTime":"2009-01-31T12:00Z"
         },
         "DateLessThan" : {
            "aws:CurrentTime":"2009-01-31T15:00Z"
         }
      }
   }]
}
```

### Example 7: Grant All Permissions to All Users in a CIDR Range<a name="grant-all-permissions-to-all-users-in-cidr-range"></a>

The following example policy grants all users \(anonymous users\) permission to use all possible Amazon SQS actions that can be shared for the queue named `111122223333/queue1`, but only if the request comes from the `192.168.143.0/24` CIDR range\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AnonymousAccess_AllActions_WhitelistIP",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:*:111122223333:queue1",
      "Condition" : {
         "IpAddress" : {
            "aws:SourceIp":"192.168.143.0/24"
         }
      }
   }]
}
```

### Example 8: Whitelist and Blacklist Permissions for Users in Different CIDR Ranges<a name="whitelist-blacklist-permissions-for-users-in-different-cidr-ranges"></a>

The following example policy has two statements:

+ The first statement grants all users \(anonymous users\) in the `192.168.143.0/24` CIDR range \(except for `192.168.143.188`\) permission to use the `SendMessage` action for the queue named `111122223333`/queue1\.

+ The second statement blacklists all users \(anonymous users\) in the `10.1.2.0/24` CIDR range from using the queue\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AnonymousAccess_SendMessage_IPLimit",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "sqs:SendMessage",
      "Resource": "arn:aws:sqs:*:111122223333:queue1",
      "Condition" : {
         "IpAddress" : {
            "aws:SourceIp":"192.168.143.0/24"
         },
         "NotIpAddress" : {
            "aws:SourceIp":"192.168.143.188/32"
         }
      }
   }, {
      "Sid":"Queue1_AnonymousAccess_AllActions_IPLimit_Deny",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:*:111122223333:queue1",
      "Condition" : {
         "IpAddress" : {
            "aws:SourceIp":"10.1.2.0/24"
         }
      }
   }]
}
```