# Using server\-side encryption \(SSE\)<a name="sqs-java-configure-sse"></a>

You can use the AWS SDK for Java to add server\-side encryption \(SSE\) to an Amazon SQS queue\. Each queue uses an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\) to generate the data encryption keys\. This example uses the AWS managed CMK for Amazon SQS\. For more information about using SSE and the role of the CMK, see [Encryption at rest](sqs-server-side-encryption.md)\. 

## Adding SSE to an existing queue<a name="sqs-java-configure-sse-existing-queue"></a>

To enable server\-side encryption for an existing queue, use the `[SetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html)` method to set the `KmsMasterKeyId` attribute\.

The following code example sets the master key as the AWS managed CMK for Amazon SQS\. The example also sets the [master key reuse period](sqs-server-side-encryption.md#sqs-sse-key-terms) to 140 seconds\.

 Before you run the example code, make sure that you have set your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-credentials.html) in the *AWS SDK for Java 2\.x Developer Guide*\. 

```
// Create an SqsClient for the specified Region.
SqsClient sqsClient = SqsClient.builder().region(Region.US_WEST_1).build();

// Get the URL of your queue.
String myQueueName = "my queue";				
GetQueueUrlResponse getQueueUrlResponse =
              sqsClient.getQueueUrl(GetQueueUrlRequest.builder().queueName(myQueueName).build());
String queueUrl = getQueueUrlResponse.queueUrl();				

// Create a hashmap for the attributes. Add the key alias and reuse period to the hashmap.
HashMap<QueueAttributeName, String> attributes = new HashMap<QueueAttributeName, String>();
final String kmsMasterKeyAlias = "alias/aws/sqs";  // the alias of the AWS managed CMK for Amazon SQS.
attributes.put(QueueAttributeName.KMS_MASTER_KEY_ID, kmsMasterKeyAlias);
attributes.put(QueueAttributeName.KMS_DATA_KEY_REUSE_PERIOD_SECONDS, "140");

// Create the SetQueueAttributesRequest.
SetQueueAttributesRequest set_attrs_request = SetQueueAttributesRequest.builder()
          .queueUrl(queueUrl)
          .attributes(attributes)
          .build();
          
sqsClient.setQueueAttributes(set_attrs_request);
```

## Disabling SSE for a queue<a name="sqs-java-disable-sse"></a>

To disable server\-side encryption for an existing queue, set the `KmsMasterKeyId` attribute to an empty string using the `SetQueueAttributes` method\.

**Important**  
`null` isn't a valid value for `KmsMasterKeyId`\.

## Creating a queue with SSE<a name="sqs-java-configure-sse-create-queue"></a>

To enable SSE when you create the queue, add the `KmsMasterKeyId` attribute to the `[CreateQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html)` API method\.

The following example creates a new queue with SSE enabled\. The queue uses the AWS managed CMK for Amazon SQS\. The example also sets the [master key reuse period](sqs-server-side-encryption.md#sqs-sse-key-terms) to 160 seconds\.

 Before you run the example code, make sure that you have set your AWS credentials\. For more information, see [Set up AWS Credentials and Region for Development](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/setup-credentials.html) in the *AWS SDK for Java 2\.x Developer Guide*\. 

```
// Create an SqsClient for the specified Region.
SqsClient sqsClient = SqsClient.builder().region(Region.US_WEST_1).build();

// Create a hashmap for the attributes. Add the key alias and reuse period to the hashmap.
HashMap<QueueAttributeName, String> attributes = new HashMap<QueueAttributeName, String>();
final String kmsMasterKeyAlias = "alias/aws/sqs";  // the alias of the AWS managed CMK for Amazon SQS.
attributes.put(QueueAttributeName.KMS_MASTER_KEY_ID, kmsMasterKeyAlias);
attributes.put(QueueAttributeName.KMS_DATA_KEY_REUSE_PERIOD_SECONDS, "140");				

// Add the attributes to the CreateQueueRequest.
CreateQueueRequest createQueueRequest =
                CreateQueueRequest.builder()
                        .queueName(queueName)
                        .attributes(attributes)
                        .build();
sqsClient.createQueue(createQueueRequest);
```

## Retrieving SSE attributes<a name="sqs-java-get-sse-attributes"></a>

For information about retrieving queue attributes, see [Examples](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html#API_GetQueueAttributes_Examples) in the *Amazon Simple Queue Service API Reference*\.

To retrieve the CMK ID or the data key reuse period for a particular queue, run the `[GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)` method and retrieve the `KmsMasterKeyId` and `KmsDataKeyReusePeriodSeconds` values\.