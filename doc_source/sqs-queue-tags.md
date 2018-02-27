# Tagging Your Amazon SQS Queues<a name="sqs-queue-tags"></a>

To organize and identify your Amazon SQS queues for cost allocation, you can add metadata *tags* that identify a queue's purpose, owner, or environment\. This is especially useful when you have many queues\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/cQhMtYX6HRI?rel=0&amp;controls=0&amp;showinfo=0/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/cQhMtYX6HRI?rel=0&amp;controls=0&amp;showinfo=0)

You can use cost allocation tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill to include tag keys and values\. For more information, see [Setting Up a Monthly Cost Allocation Report](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html#allocation-report) in the *AWS Billing and Cost Management User Guide*\.

## Overview<a name="sqs-queue-tags-overview"></a>

Each tag consists of a key\-value pair that you define\. For example, you can easily identify your *production* and *testing* queues if you tag your queues as follows:


****  

| Queue | Key | Value | 
| --- | --- | --- | 
| MyQueueA | QueueType | Production | 
| MyQueueB | QueueType | Testing | 

**Note**  
When you use queue tags, keep the following guidelines in mind:  
We don't recommend adding more than 50 tags to a queue\.
Tags don't have any semantic meaning\. Amazon SQS interprets tags as character strings\.
Tags are case\-sensitive\.
A new tag with a key identical to that of an existing tag overwrites the existing tag\.
Tagging API actions are limited to 5 TPS per AWS account\. If your application requires a higher throughput, file a [technical support request](https://console.aws.amazon.com/support/home#/case/create?issueType=technical)\.
For a full list of tag restrictions, see [Limits Related to Queues](sqs-limits.md#limits-queues)\.

You can't add tags to a new queue when you create it using the AWS Management Console \(you *can* add tags after the queue is created\)\. However, you can add, update, or remove queue tags at any time using the Amazon SQS API actions\.

## Getting Started with Tagging<a name="sqs-queue-tags-getting-started"></a>

For information on how to manage Amazon SQS queue tags using the AWS Management Console or API actions, see the [Adding, Updating, and Removing Tags from an Amazon SQS Queue](sqs-add-update-remove-tag-queue.md) tutorial\.

You can list, add, update, or remove tags for an Amazon SQS queue using the following API actions:


****  

| Task | API Action | 
| --- | --- | 
| Add tags to a queue or update the tags added to a queue | [TagQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_TagQueue.html) | 
| Remove tags from a queue | [UntagQueue](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_UntagQueue.html) | 
| List the tags added to a queue | [ListQueueTags](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ListQueueTags.html) | 