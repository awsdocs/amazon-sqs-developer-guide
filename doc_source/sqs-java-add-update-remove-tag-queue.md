# Configuring tags for a queue<a name="sqs-java-add-update-remove-tag-queue"></a>

Use cost\-allocation tags to help organize and identify your Amazon SQS queues\. The following examples show how to configure tags using the AWS SDK for Java\. For more information, see [Amazon SQS cost allocation tags](sqs-queue-tags.md)\.

 Before you run the example code, make sure that you have set your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-credentials.html) in the *AWS SDK for Java 2\.x Developer Guide*\. 

## Listing tags<a name="sqs-java-list-tags"></a>

To list the tags for a queue, use the `ListQueueTags` method\.

```
// Create an SqsClient for the specified region.
SqsClient sqsClient = SqsClient.builder().region(Region.US_WEST_1).build();

// Get the queue URL.
String queueName = "MyStandardQ1";
GetQueueUrlResponse getQueueUrlResponse =
        sqsClient.getQueueUrl(GetQueueUrlRequest.builder().queueName(queueName).build());
String queueUrl = getQueueUrlResponse.queueUrl();

// Create the ListQueueTagsRequest.
final ListQueueTagsRequest listQueueTagsRequest = 
                                  ListQueueTagsRequest.builder().queueUrl(queueUrl).build();

// Retrieve the list of queue tags and print them.
final ListQueueTagsResponse listQueueTagsResponse =
                                  sqsClient.listQueueTags(listQueueTagsRequest);
System.out.println(String.format("ListQueueTags: \tTags for queue %s are %s.\n",
                queueName, listQueueTagsResponse.tags() ));
```

## Adding or updating tags<a name="sqs-java-add-tags"></a>

To add or update tag values for a queue, use the `TagQueue` method\.

```
 // Create an SqsClient for the specified Region.
SqsClient sqsClient = SqsClient.builder().region(Region.US_WEST_1).build();

// Get the queue URL.
String queueName = "MyStandardQ1";
GetQueueUrlResponse getQueueUrlResponse =
        sqsClient.getQueueUrl(GetQueueUrlRequest.builder().queueName(queueName).build());
String queueUrl = getQueueUrlResponse.queueUrl();	

// Build a hashmap of the tags.
final HashMap<String, String> addedTags = new HashMap<>();
        addedTags.put("Team", "Development");
        addedTags.put("Priority", "Beta");
        addedTags.put("Accounting ID", "456def");

//Create the TagQueueRequest and add them to the queue.
final TagQueueRequest tagQueueRequest = TagQueueRequest.builder()
        .queueUrl(queueUrl)
        .tags(addedTags)
        .build();
sqsClient.tagQueue(tagQueueRequest);
```

## Removing tags<a name="sqs-java-remove-tags"></a>

To remove one or more tags from the queue, use the `UntagQueue` method\. The following example removes the `Accounting ID` tag\.

```
 
// Create the UntagQueueRequest.
final UntagQueueRequest untagQueueRequest = UntagQueueRequest.builder()
        .queueUrl(queueUrl)
        .tagKeys("Accounting ID")
        .build();
        
// Remove the tag from this queue.
sqsClient.untagQueue(untagQueueRequest);
```