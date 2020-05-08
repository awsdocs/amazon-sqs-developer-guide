# Automating notifications from AWS services to Amazon SQS using CloudWatch Events<a name="sqs-automating-using-cloudwatch-events"></a>

Amazon CloudWatch Events lets you automate AWS services and respond to system events such as application availability issues or resource changes\. Events from AWS services are delivered to CloudWatch Events nearly in real time\. You can write simple rules to indicate which events are of interest to you and what automated actions to take when an event matches a rule\.

CloudWatch Events lets you set a variety of *targets*—such as Amazon SQS standard and FIFO queues—which receive events in JSON format\. For more information, see the *[Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)*\.