# Creating an Amazon VPC Endpoint Policy for Amazon SQS<a name="sqs-vpc-endpoint-policy"></a>

You can create a policy for Amazon VPC endpoints for Amazon SQS in which you specify the following:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources on which actions can be performed\.

For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*

The following example VPC endpoint policy specifies that the IAM user `MyUser` is allowed to send messages to the Amazon SQS queue `MyQueue`\.

```
{
   "Statement": [{
      "Action": ["sqs:SendMessage"],
      "Effect": "Allow",
      "Resource": "arn:aws:sqs:us-east-2:123456789012:MyQueue",
      "Principal": "arn:aws:iam:123456789012us-east:user/MyUser"
   }]
}
```

The following are denied:
+ Other Amazon SQS API actions, such as `sns:CreateQueue` and `sns:DeleteQueue`\.
+ Other IAM users and rules which attempt to use this VPC endpoint\.
+ `MyUser` sending messages to a different Amazon SQS queue\.

**Note**  
The IAM user can still use other Amazon SQS API actions from *outside* the VPC\. For more information, see [Example 5: Deny Access if It Isn't from a VPC Endpoint](sqs-creating-custom-policies-access-policy-examples.md#deny-not-from-vpc)\.