# Compatibility<a name="FIFO-compatibility"></a>

**Clients**  
The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues\.

**Services**  
If your application uses multiple AWS services, or a mix of AWS and external services, it is important to understand which service functionality doesn't support FIFO queues\.  
Some AWS or external services that send notifications to Amazon SQS might not be compatible with FIFO queues, despite allowing you to set a FIFO queue as a target\.  
The following features of AWS services aren't currently compatible with FIFO queues:  
+ [Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html)
+ [Auto Scaling Lifecycle Hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)
+ [AWS IoT Rule Actions](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html)
+ [AWS Lambda Dead\-Letter Queues](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html#dlq)
For information about compatibility of other services with FIFO queues, see your service documentation\.