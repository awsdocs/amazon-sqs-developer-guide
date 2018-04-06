# Using Custom Policies with the Amazon SQS Access Policy Language<a name="sqs-creating-custom-policies"></a>

If you want to allow Amazon SQS access based only on an AWS account ID and basic permissions \(such as for [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) or [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)\), you don't need to write your own policies\. You can just use the Amazon SQS [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action\.

If you want to explicitly deny or allow access based on more specific conditions \(such as the time the request comes in or the IP address of the requester\), you need to write your own Amazon SQS policies and upload them to the AWS system using the Amazon SQS `SetQueueAttributes` action\.


+ [Amazon SQS Access Control Architecture](sqs-creating-custom-policies-architecture.md)
+ [Amazon SQS Access Control Process Workflow](sqs-creating-custom-policies-process-workflow.md)
+ [Amazon SQS Access Policy Language Key Concepts](sqs-creating-custom-policies-key-concepts.md)
+ [Amazon SQS Access Policy Language Evaluation Logic](sqs-creating-custom-policies-evaluation-logic.md)
+ [Relationships Between Explicit and Default Denials in the Amazon SQS Access Policy Language](sqs-creating-custom-policies-relationships-between-explicit-default-denials.md)
+ [Custom Amazon SQS Access Policy Language Examples](sqs-creating-custom-policies-access-policy-examples.md)