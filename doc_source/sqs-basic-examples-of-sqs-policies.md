# Basic examples of Amazon SQS policies<a name="sqs-basic-examples-of-sqs-policies"></a>

This section shows example policies for common Amazon SQS use cases\.

You can use the console to verify the effects of each policy as you attach the policy to the user\. Initially, the user doesn't have permissions and won't be able to do anything in the console\. As you attach policies to the user, you can verify that the user can perform various actions in the console\.

**Note**  
We recommend that you use two browser windows: one to grant permissions and the other to sign into the AWS Management Console using the user's credentials to verify permissions as you grant them to the user\.

## Example 1: Grant one permission to one AWS account<a name="grant-one-permission-to-one-account"></a>

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

## Example 2: Grant two permissions to one AWS account<a name="grant-two-permissions-to-one-account"></a>

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

## Example 3: Grant all permissions to two AWS accounts<a name="grant-all-permissions-to-two-accounts"></a>

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

## Example 4: Grant cross\-account permissions to a role and a user name<a name="grant-cross-account-permissions-to-role-and-user-name"></a>

The following example policy grants `role1` and `username1` under AWS account number `111122223333` cross\-account permission to use all actions to which Amazon SQS allows shared access for the queue named `123456789012/queue1` in the US East \(Ohio\) region\.

Cross\-account permissions don't apply to the following actions:
+ `[AddPermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html)`
+ `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`
+ `[DeleteQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html)`
+ `[ListQueues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueues.html)`
+ `[ListQueueTags](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueueTags.html)`
+ `[RemovePermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_RemovePermission.html)`
+ `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`
+ `[TagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_TagQueue.html)`
+ `[UntagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_UntagQueue.html)`

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

## Example 5: Grant a permission to all users<a name="grant-permissions-to-all-users"></a>

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

## Example 6: Grant a time\-limited permission to all users<a name="grant-time-limited-permission-to-all-users"></a>

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

## Example 7: Grant all permissions to all users in a CIDR range<a name="grant-all-permissions-to-all-users-in-cidr-range"></a>

The following example policy grants all users \(anonymous users\) permission to use all possible Amazon SQS actions that can be shared for the queue named `111122223333/queue1`, but only if the request comes from the `192.168.143.0/24` CIDR range\.

```
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AnonymousAccess_AllActions_AllowlistIP",
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

## Example 8: Allowlist and blocklist permissions for users in different CIDR ranges<a name="allowlist-blocklist-permissions-for-users-in-different-cidr-ranges"></a>

The following example policy has two statements:
+ The first statement grants all users \(anonymous users\) in the `192.168.143.0/24` CIDR range \(except for `192.168.143.188`\) permission to use the `SendMessage` action for the queue named `111122223333`/queue1\.
+ The second statement blocks all users \(anonymous users\) in the `10.1.2.0/24` CIDR range from using the queue\.

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