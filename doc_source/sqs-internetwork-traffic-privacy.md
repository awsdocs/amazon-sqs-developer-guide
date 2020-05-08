# Internetwork traffic privacy<a name="sqs-internetwork-traffic-privacy"></a>

An Amazon Virtual Private Cloud \(Amazon VPC\) endpoint for Amazon SQS is a logical entity within a VPC that allows connectivity only to Amazon SQS\. The VPC routes requests to Amazon SQS and routes responses back to the VPC\. The following sections provide information about working with VPC endpoints and creating VPC endpoint policies\.

**Topics**
+ [Amazon Virtual Private Cloud endpoints for Amazon SQS](#sqs-vpc-endpoints)
+ [Creating an Amazon VPC endpoint policy for Amazon SQS](#sqs-vpc-endpoint-policy)

## Amazon Virtual Private Cloud endpoints for Amazon SQS<a name="sqs-vpc-endpoints"></a>

If you use Amazon VPC to host your AWS resources, you can establish a connection between your VPC and Amazon SQS\. You can use this connection to send messages to your Amazon SQS queues without crossing the public internet\.

Amazon VPC lets you launch AWS resources in a custom virtual network\. You can use a VPC to control your network settings, such as the IP address range, subnets, route tables, and network gateways\. For more information about VPCs, see the *[Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)*\.

To connect your VPC to Amazon SQS, you must first define an *interface VPC endpoint*, which lets you connect your VPC to other AWS services\. The endpoint provides reliable, scalable connectivity to Amazon SQS without requiring an internet gateway, network address translation \(NAT\) instance, or VPN connection\. For more information, see [Tutorial: Sending a message to an Amazon SQS queue from Amazon Virtual Private Cloud](sqs-sending-messages-from-vpc.md) and [Example 5: Deny access if it isn't from a VPC endpoint](sqs-creating-custom-policies-access-policy-examples.md#deny-not-from-vpc) in this guide and [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.

**Important**  
You can use Amazon Virtual Private Cloud only with HTTPS Amazon SQS endpoints\.
When you configure Amazon SQS to send messages from Amazon VPC, you must enable private DNS and specify endpoints in the format `sqs.us-east-2.amazonaws.com`\.
Private DNS doesn't support legacy endpoints such as `queue.amazonaws.com` or `us-east-2.queue.amazonaws.com`\.

## Creating an Amazon VPC endpoint policy for Amazon SQS<a name="sqs-vpc-endpoint-policy"></a>

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
      "Principal": {
        "AWS": "arn:aws:iam:123456789012:user/MyUser"
      }
   }]
}
```

The following are denied:
+ Other Amazon SQS API actions, such as `sqs:CreateQueue` and `sqs:DeleteQueue`\.
+ Other IAM users and rules which attempt to use this VPC endpoint\.
+ `MyUser` sending messages to a different Amazon SQS queue\.

**Note**  
The IAM user can still use other Amazon SQS API actions from *outside* the VPC\. For more information, see [Example 5: Deny access if it isn't from a VPC endpoint](sqs-creating-custom-policies-access-policy-examples.md#deny-not-from-vpc)\.