# Amazon SQS API Permissions: Actions and Resource Reference<a name="sqs-api-permissions-reference"></a>

When you set up [Access Control](sqs-authentication-and-access-control.md#access-control) and write permissions policies that you can attach to an IAM identity, you can use the following table as a reference\. The table lists each Amazon Simple Queue Service action, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\.

Specify the actions in the policy's `Action` field, and the resource value in the policy's `Resource` field\. To specify an action, use the `sqs:` prefix followed by the action name \(for example, `sqs:CreateQueue`\)\.

Currently, Amazon SQS supports only a limited subset of the [condition keys available in IAM](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys):

+ `aws:CurrentTime`

+ `aws:EpochTime`

+ `aws:SecureTransport`

+ `aws:SourceArn`

+ `aws:SourceIP`

+ `aws:UserAgent`

+ `aws:MultiFactorAuthAge`

+ `aws:MultiFactorAuthPresent`

+ `aws:TokenAge`

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


**Amazon Simple Queue Service API and Required Permissions for Actions**  
<a name="sqs-api-and-required-permissions-for-actions-table"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-api-permissions-reference.html)