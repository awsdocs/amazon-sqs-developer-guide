# Custom Amazon SQS Access Policy Language examples<a name="sqs-creating-custom-policies-access-policy-examples"></a>

The following are examples of typical Amazon SQS access policies\.

## Example 1: Give permission to one account<a name="one-account"></a>

The following example Amazon SQS policy gives AWS account 111122223333 permission to send to and receive from `queue2` owned by AWS account 444455556666\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase1",
   "Statement" : [{
      "Sid": "1", 
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
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"  
   }]
}
```

## Example 2: Give permission to one or more accounts<a name="two-accounts"></a>

The following example Amazon SQS policy gives one or more AWS accounts access to queues owned by your account for a specific time period\. It is necessary to write this policy and to upload it to Amazon SQS using the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying a time restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase2",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333",
            "444455556666"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         }
      }   
   }]
}
```

## Example 3: Give permission to requests from Amazon EC2 instances<a name="requests-from-ec2"></a>

The following example Amazon SQS policy gives access to requests that come from Amazon EC2 instances\. This example builds on the "[Example 2: Give permission to one or more accounts](#two-accounts)" example: it restricts access to before June 30, 2009 at 12 noon \(UTC\), it restricts access to the IP range `203.0.113.0/24`\. It is necessary to write this policy and to upload it to Amazon SQS using the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying an IP address restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase3",
   "Statement" : [{
      "Sid": "1", 
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
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         },
         "IpAddress": {
            "AWS:SourceIp": "203.0.113.0/24"
         }
      }   
   }]
}
```

## Example 4: Deny access to a specific account<a name="deny-account"></a>

The following example Amazon SQS policy denies a specific AWS account access to your queue\. This example builds on the "[Example 1: Give permission to one account](#one-account)" example: it denies access to the specified AWS account\. It is necessary to write this policy and to upload it to Amazon SQS using the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit deny access to a queue \(it allows only granting access to a queue\)\. 

```
{ 
   "Version": "2012-10-17",
   "Id": "UseCase4",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Deny",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"   
   }]
}
```

## Example 5: Deny access if it isn't from a VPC endpoint<a name="deny-not-from-vpc"></a>

The following example Amazon SQS policy restricts access to `queue1`: 111122223333 can perform the [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) and [https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) actions only from the VPC endpoint ID `vpce-1a2b3c4d` \(specified using the `aws:sourceVpce` condition\)\. For more information, see [Amazon Virtual Private Cloud endpoints for Amazon SQS](sqs-internetwork-traffic-privacy.md#sqs-vpc-endpoints)\.

**Note**  
The `aws:sourceVpce` condition doesn't require an ARN for the VPC endpoint resource, only the VPC endpoint ID\.
You can modify the following example to restrict all actions to a specific VPC endpoint by denying all Amazon SQS actions \(`sqs:*`\) in the second statement\. However, such a policy statement would stipulate that all actions \(including administrative actions needed to modify queue permissions\) must be made through the specific VPC endpoint defined in the policy, potentially preventing the user from modifying queue permissions in the future\.

```
{
   "Version": "2012-10-17",
   "Id": "UseCase5",
   "Statement": [{
      "Sid": "1",
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
         "Resource": "arn:aws::sqs:us-east-2:111122223333:queue1"
      },
      {
         "Sid": "2",
         "Effect": "Deny",
         "Principal": "*",
         "Action": [
            "sqs:SendMessage",
            "sqs:ReceiveMessage"
         ],
         "Resource": "arn:aws::sqs:us-east-2:111122223333:queue1",
         "Condition": {
            "StringNotEquals": {
               "aws:sourceVpce": "vpce-1a2b3c4d"
            }
         }
      }
   ]
}
```