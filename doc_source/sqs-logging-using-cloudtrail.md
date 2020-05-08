# Logging Amazon SQS API calls using AWS CloudTrail<a name="sqs-logging-using-cloudtrail"></a>

Amazon SQS is integrated with AWS CloudTrail, a service that provides a record of the Amazon SQS calls that a user, role, or AWS service makes\. CloudTrail captures API calls related to Amazon SQS queues as events, including calls from the Amazon SQS console and code calls from Amazon SQS APIs\. For more information about CloudTrail, see the [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Note**  
CloudTrail logging is supported for both standard and FIFO queues\.

Using the information that CloudTrail collects, you can identify a specific request to an Amazon SQS API, the IP address of the requester, the requester's identity, the date and time of the request, and so on\. If you configure a *trail*, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket\. If you don't configure a trail, you can view the most recent events in the event history in the CloudTrail console\. For more information, see [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html) in the *[AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)*\.

## Amazon SQS information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

When you create your AWS account, CloudTrail is enabled\. When a supported Amazon SQS event activity occurs, it is recorded in a CloudTrail event with other AWS service events in the event history\. You can view, search, and download recent events for your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html) in the *AWS CloudTrail User Guide*\.

A trail allows CloudTrail to deliver log files to an Amazon S3 bucket\. You can create a trail to keep an ongoing record of events in your AWS account\. By default, when you create a trail using the AWS Management Console, the trail applies to all AWS Regions\. The trail logs events from all AWS Regions and delivers log files to the specified Amazon S3 bucket\. You can also configure other AWS services to further analyze and act on the event data collected in CloudTrail logs\. For more information, see the following topics in the *AWS CloudTrail User Guide*: 
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)
+ [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

Amazon SQS supports logging the following actions:
+ `[AddPermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html)`
+ `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)`
+ `[DeleteQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteQueue.html)`
+ `[PurgeQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_PurgeQueue.html)`
+ `[RemovePermission](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_RemovePermission.html)`
+ `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)`
+ `[TagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_TagQueue.html)`
+ `[UntagQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_UntagQueue.html)`

Every event or log entry contains information about the requester\. This information helps you determine the following: 
+ Was the request made with root or IAM user credentials?
+ Was the request made with temporary security credentials for a role or a federated user?
+ Was the request made by another AWS service?

For more information, see [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html) in the *AWS CloudTrail User Guide*\.

## Example Amazon SQS log file entries<a name="understanding-service-name-entries"></a>

CloudTrail log files contain one or more log entries where each entry is made up of multiple JSON\-formatted events\. A log entry represents a single request from any source and includes information about the requested action, any parameters, the date and time of the action, and so on\. The log entries aren't guaranteed to be in any particular order\. That is, they're not an ordered stack trace of the public API calls\.

### AddPermission<a name="add-permission"></a>

The following example shows a CloudTrail log entry for an `AddPermission` API call\.

```
{
	  "Records": [
	    {
	      "eventVersion": "1.06",
	      "userIdentity": {
	        "type": "IAMUser",
	        "principalId": "AKIAI44QH8DHBEXAMPLE",
	        "arn": "arn:aws:iam::123456789012:user/Alice",
	        "accountId": "123456789012",
	        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
	        "userName": "Alice"
	      },
	      "eventTime": "2018-06-28T22:23:46Z",
	      "eventSource": "sqs.amazonaws.com",
	      "eventName": "AddPermission",
	      "awsRegion": "us-east-2",
	      "sourceIPAddress": "203.0.113.0",
	      "userAgent": "Mozilla/5.0 (X11; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0",
	      "requestParameters": {
	        "actions": [
	          "SendMessage"
	        ],
	        "AWSAccountIds": [
	          "123456789012"
	        ],
	        "label": "MyLabel",
	        "queueUrl": "https://sqs.us-east-2.amazon.com/123456789012/MyQueue"
	      },
	      "responseElements": null,
	      "requestID": "123abcde-f4gh-50ij-klmn-60o789012p30",
	      "eventID": "0987g654-32f1-09e8-d765-c4f3fb2109fa"
	    }
	  ]
	}
```

### CreateQueue<a name="create-queue"></a>

The following example shows a CloudTrail log entry for a `CreateQueue` API call\.

```
{
	  "Records": [
	    {
	      "eventVersion": "1.06",
	      "userIdentity": {
	        "type": "IAMUser",
	        "principalId": "AKIAI44QH8DHBEXAMPLE",
	        "arn": "arn:aws:iam::123456789012:user/Alejandro",
	        "accountId": "123456789012",
	        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
	        "userName": "Alejandro"
	      },
	      "eventTime": "2018-06-28T22:23:46Z",
	      "eventSource": "sqs.amazonaws.com",
	      "eventName": "CreateQueue",
	      "awsRegion": "us-east-2",
	      "sourceIPAddress": "203.0.113.1",
	      "userAgent": "Mozilla/5.0 (X11; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0",
	      "requestParameters": {
	        "queueName": "MyQueue"
	      },
	      "responseElements": {
	        "queueUrl": "https://sqs.us-east-2.amazon.com/123456789012/MyQueue"
	      },
	      "requestID": "123abcde-f4gh-50ij-klmn-60o789012p30",
	      "eventID": "0987g654-32f1-09e8-d765-c4f3fb2109fa"
	    }
	  ]
	}
```

### DeleteQueue<a name="delete-queue"></a>

The following example shows a CloudTrail log entry for a `DeleteQueue` API call\.

```
{
	  "Records": [
	    {
	      "eventVersion": "1.06",
	      "userIdentity": {
	        "type": "IAMUser",
	        "principalId": "AKIAI44QH8DHBEXAMPLE",
	        "arn": "arn:aws:iam::123456789012:user/Carlos",
	        "accountId": "123456789012",
	        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
	        "userName": "Carlos"
	      },
	      "eventTime": "2018-06-28T22:23:46Z",
	      "eventSource": "sqs.amazonaws.com",
	      "eventName": "DeleteQueue",
	      "awsRegion": "us-east-2",
	      "sourceIPAddress": "203.0.113.2",
	      "userAgent": "Mozilla/5.0 (X11; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0",
	      "requestParameters": {
	        "queueUrl": "https://sqs.us-east-2.amazon.com/123456789012/MyQueue"
	      },
	      "responseElements": null,
	      "requestID": "123abcde-f4gh-50ij-klmn-60o789012p30",
	      "eventID": "0987g654-32f1-09e8-d765-c4f3fb2109fa"
	    }
	  ]
	}
```

### RemovePermission<a name="removepermission"></a>

The following example shows a CloudTrail log entry for a `RemovePermission` API call\.

```
{
	  "Records": [
	    {
	      "eventVersion": "1.06",
	      "userIdentity": {
	        "type": "IAMUser",
	        "principalId": "AKIAI44QH8DHBEXAMPLE",
	        "arn": "arn:aws:iam::123456789012:user/Jane",
	        "accountId": "123456789012",
	        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
	        "userName": "Jane"
	      },
	      "eventTime": "2018-06-28T22:23:46Z",
	      "eventSource": "sqs.amazonaws.com",
	      "eventName": "RemovePermission",
	      "awsRegion": "us-east-2",
	      "sourceIPAddress": "203.0.113.3",
	      "userAgent": "Mozilla/5.0 (X11; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0",
	      "requestParameters": {
	        "label": "label",
	        "queueUrl": "https://sqs.us-east-2.amazon.com/123456789012/MyQueue"
	      },
	      "responseElements": null,
	      "requestID": "123abcde-f4gh-50ij-klmn-60o789012p30",
	      "eventID": "0987g654-32f1-09e8-d765-c4f3fb2109fa"
	    }
	  ]
	}
```

### SetQueueAttributes<a name="set-queue-attributes"></a>

The following example shows a CloudTrail log entry for `SetQueueAttributes`:

```
{
	  "Records": [
	    {
	      "eventVersion": "1.06",
	      "userIdentity": {
	        "type": "IAMUser",
	        "principalId": "AKIAI44QH8DHBEXAMPLE",
	        "arn": "arn:aws:iam::123456789012:user/Maria",
	        "accountId": "123456789012",
	        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
	        "userName": "Maria"
	      },
	      "eventTime": "2018-06-28T22:23:46Z",
	      "eventSource": "sqs.amazonaws.com",
	      "eventName": "SetQueueAttributes",
	      "awsRegion": "us-east-2",
	      "sourceIPAddress": "203.0.113.4",
	      "userAgent": "Mozilla/5.0 (X11; Linux x86_64; rv:24.0) Gecko/20100101 Firefox/24.0",
	      "requestParameters": {
	        "attributes": {
	          "VisibilityTimeout": "100"
	        },
	        "queueUrl": "https://sqs.us-east-2.amazon.com/123456789012/MyQueue"
	      },
	      "responseElements": null,
	      "requestID": "123abcde-f4gh-50ij-klmn-60o789012p30",
	      "eventID": "0987g654-32f1-09e8-d765-c4f3fb2109fa"
	    }
	  ]
	}
```