# Amazon SQS API permissions: Actions and resource reference<a name="sqs-api-permissions-reference"></a>

When you set up [Access control](sqs-authentication-and-access-control.md#access-control) and write permissions policies that you can attach to an IAM identity, you can use the following table as a reference\. The table lists each Amazon Simple Queue Service action, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\.

Specify the actions in the policy's `Action` field, and the resource value in the policy's `Resource` field\. To specify an action, use the `sqs:` prefix followed by the action name \(for example, `sqs:CreateQueue`\)\.

Currently, Amazon SQS supports only a limited subset of the [condition keys available in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys):
+ `aws:CurrentTime`
+ `aws:EpochTime`
+ `aws:SecureTransport`
+ `aws:SourceArn`
**Note**  
This condition ensures that AWS services grant access only on behalf of resources that your AWS account owns\. You can't specify the ARN of an IAM role as source ARN, because an IAM role is neither a source nor a service\.
+ `aws:SourceIP`
+ `aws:UserAgent`
+ `aws:MultiFactorAuthAge`
+ `aws:MultiFactorAuthPresent`
+ `aws:PrincipalOrgID`
+ `aws:RequestTag`
+ `aws:sourceVpce`
+ `aws:TagKeys`
+ `aws:TokenAge`

Use the scroll bars to see the rest of the table\.


**Amazon Simple Queue Service API and required permissions for actions**  
<a name="sqs-api-and-required-permissions-for-actions-table"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-api-permissions-reference.html)