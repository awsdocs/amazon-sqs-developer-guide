# Tutorial: Adding, Updating, and Removing Cost Allocation Tags for an Amazon SQS Queue<a name="sqs-add-update-remove-tag-queue"></a>

You can add cost allocation tags to your Amazon SQS queues to help organize and identify them\. The following example demonstrates adding, updating, and removing tags for a queue\. For a more information, see [Amazon SQS Cost Allocation Tags](sqs-queue-tags.md)\.


+ [To update or remove a tag added to an Amazon SQS queue using the AWS Management Console](#sqs-update-remove-tag-queue-console)
+ [AWS SDK for Java](#sqs-add-update-remove-tag-queue-java)

## AWS Management Console<a name="sqs-update-remove-tag-queue-console"></a>

The following steps assume that you already [created an Amazon SQS queue](sqs-create-queue.md)\.

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the queue list, select a queue\.  
![\[A list of queues in the Amazon SQS console\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-sending-message-to-queue-select-queue.png)

1. Choose the **Tags** tab\.

   The tags added to the queue are listed\.  
![\[A list of tags on the Tags tab in the Amazon SQS console\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-tutorials-managing-queue-tags-list-tags.png)

1. Choose **Add/Edit Tags**\.

1. Modify queue tags:

   + To add a tag, choose **Add New Tag**, enter a **Key** and **Value**, and then choose **Apply Changes**\.

   + To update a tag, change its **Key** and **Value** and then choose **Apply Changes**\.

   + To remove a tag, choose ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-delete-queue-tag.png) next to a key\-value pair and then choose **Apply Changes**\.

   The queue tag changes are applied\.

## AWS SDK for Java<a name="sqs-add-update-remove-tag-queue-java"></a>

Before you begin working with the example code, specify your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html) in the *AWS SDK for Java Developer Guide*\.

### To add, update, and remove tags from a queue<a name="sqs-add-update-remove-tag-queue-java-update-remove"></a>

1. Copy the example program for a [standard queue](standard-queues-getting-started-java.md) or a [FIFO queue](FIFO-queues-getting-started-java.md)\.

1. To list the tags added to a queue, add the following code which uses the `ListQueueTags` API action:

   ```
   final ListQueueTagsRequest listQueueTagsRequest = new ListQueueTagsRequest(queueUrl);
   final ListQueueTagsResult listQueueTagsResult = SQSClientFactory.newSQSClient().listQueueTags(listQueueTagsRequest);
   System.out.println(String.format("ListQueueTags: \tTags for queue %s are %s.\n", QUEUE_NAME, listQueueTagsResult.getTags()))
   ```

1. To add or update the values of the queue's tags using the tag's key, add the following code which uses the `TagQueue` API action:

   ```
   final Map<String, String> addedTags = new HashMap<>();
   addedTags.put("Team", "Development");
   addedTags.put("Priority", "Beta");
   addedTags.put("Accounting ID", "456def");
   final TagQueueRequest tagQueueRequest = new TagQueueRequest(queueUrl, addedTags);
   
   System.out.println(String.format("TagQueue: \t\tAdd tags %s to queue %s.\n", addedTags, QUEUE_NAME));
   SQSClientFactory.newSQSClient().tagQueue(tagQueueRequest);
   ```

1. To remove a tag from the queue using the tag's key, add the following code which uses the `UntagQueue` API action:

   ```
   final List<String> tagKeys = Arrays.asList("Accounting ID");
   final UntagQueueRequest untagQueueRequest = new UntagQueueRequest(queueUrl, tagKeys);
   System.out.println(String.format("UntagQueue: \tRemove tags %s from queue %s.\n", tagKeys, QUEUE_NAME));
   SQSClientFactory.newSQSClient().untagQueue(untagQueueRequest);
   ```

1. Compile and run your program\.

   The existing tags are listed, three are updated, and one tag is removed from the queue\.