# Amazon SQS access control process workflow<a name="sqs-creating-custom-policies-process-workflow"></a>

The following diagram describes the general workflow of access control with the Amazon SQS access policy language\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Basic_Flow.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) You write an Amazon SQS policy for your queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) You upload your policy to AWS\. The AWS service provides an API that you use to upload your policies\. For example, you use the Amazon SQS `SetQueueAttributes` action to upload a policy for a particular Amazon SQS queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) Someone sends a request to use your Amazon SQS queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-4-red.png) Amazon SQS examines all available Amazon SQS policies and determines which ones are applicable\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-5-red.png) Amazon SQS evaluates the policies and determines whether the requester is allowed to use your queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-6-red.png) Based on the policy evaluation result, Amazon SQS either returns an `Access denied` error to the requester or continues to process the request\.