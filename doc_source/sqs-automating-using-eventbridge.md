# Automating notifications from AWS services to Amazon SQS using Amazon EventBridge<a name="sqs-automating-using-eventbridge"></a>

Amazon EventBridge lets you automate AWS services and respond to system events such as application availability issues or resource changes\. Events from AWS services are delivered to EventBridge nearly in real time\. You can write simple rules to indicate which events are of interest to you and what automated actions to take when an event matches a rule\.

EventBridge lets you set a variety of *targets*—such as Amazon SQS standard and FIFO queues—which receive events in JSON format\. For more information, see the *[Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)*\.