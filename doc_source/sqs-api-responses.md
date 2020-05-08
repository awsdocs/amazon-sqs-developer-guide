# Interpreting responses<a name="sqs-api-responses"></a>

In response to an action request, Amazon SQS returns an XML data structure that contains the results of the request\. For more information, see the individual actions in the *[Amazon Simple Queue Service API Reference](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

**Topics**
+ [Successful response structure](#sqs-api-successful-response-structure)
+ [Error response structure](#sqs-api-error-response-structure)

## Successful response structure<a name="sqs-api-successful-response-structure"></a>

If the request is successful, the main response element is named after the action, with `Response` appended \(`ActionNameResponse`\)\.

This element contains the following child elements:
+ **`ActionNameResult`** – Contains an action\-specific element\. For example, the `CreateQueueResult` element contains the `QueueUrl` element which, in turn, contains the URL of the created queue\.
+ **`ResponseMetadata`** – Contains the `RequestId` which, in turn, contains the UUID of the request\.

The following is an example successful response in XML format:

```
<CreateQueueResponse
   xmlns=https://sqs.us-east-2.amazonaws.com/doc/2012-11-05/
   xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance
   xsi:type=CreateQueueResponse>
   <CreateQueueResult>
      <QueueUrl>https://sqs.us-east-2.amazonaws.com/770098461991/queue2</QueueUrl>
   </CreateQueueResult>
   <ResponseMetadata>
      <RequestId>cb919c0a-9bce-4afe-9b48-9bdf2412bb67</RequestId>
   </ResponseMetadata>
</CreateQueueResponse>
```

## Error response structure<a name="sqs-api-error-response-structure"></a>

If a request is unsuccessful, Amazon SQS always returns the main response element `ErrorResponse`\. This element contains an `Error` element and a `RequestId` element\.

The `Error` element contains the following child elements:
+ **`Type`** – Specifies whether the error was a producer or consumer error\.
+ **`Code`** – Specifies the type of error\.
+ **`Message`** – Specifies the error condition in a readable format\.
+ **`Detail`** – \(Optional\) Specifies additional details about the error\.

The `RequestId` element contains the UUID of the request\.

The following is an example error response in XML format:

```
<ErrorResponse>
   <Error>
      <Type>Sender</Type>
      <Code>InvalidParameterValue</Code>
      <Message>
         Value (quename_nonalpha) for parameter QueueName is invalid.
         Must be an alphanumeric String of 1 to 80 in length.
      </Message>
   </Error>
   <RequestId>42d59b56-7407-4c4a-be0f-4c88daeea257</RequestId>
</ErrorResponse>
```