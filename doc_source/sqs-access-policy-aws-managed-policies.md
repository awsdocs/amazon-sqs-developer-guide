# AWS managed \(predefined\) policies for Amazon SQS<a name="sqs-access-policy-aws-managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are for using Amazon SQS management APIs:
+ **[AmazonSQSFullAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonSQSFullAccess)** – Grants full access to Amazon SQS queues using the AWS Management Console\.
+ **[AmazonSQSReadOnlyAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonSQSReadOnlyAccess)** – Grants read\-only access to Amazon SQS queues using the AWS Management Console\.

You can search and review available policies on the IAM console\. You can also create your own custom IAM policies to allow permissions for Amazon SQS actions and queues\. You can attach these custom policies to the IAM users or groups that require permissions\. 