# Making Query API Requests<a name="sqs-making-api-requests"></a>

In this section you'll learn how to construct an Amazon SQS endpoint, make `GET` and `POST` requests and interpret responses\.


+ [Constructing an Endpoint](#sqs-api-constructing-endpoints)
+ [Making a GET Request](#structure-get-request)
+ [Making a POST Request](#structure-post-request)
+ [Authenticating Requests](#sqs-api-request-authentication)
+ [Interpreting Responses](#sqs-api-responses)

## Constructing an Endpoint<a name="sqs-api-constructing-endpoints"></a>

In order to work with Amazon SQS queues, you must construct an endpoint\. For information about [region\-specific Amazon SQS endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#sqs_region), see the *Amazon Web Services General Reference*\.

Every Amazon SQS endpoint is independent\. For example, if two queues are named `MyQueue` and one has the endpoint `sqs.us-east-2.amazonaws.com` while the other has the endpoint `sqs.eu-west-2.amazonaws.com`, the two queues don't share any data with each other\.

The following is an example of an endpoint which makes a request to create a queue\. 

```
https://sqs.eu-west-2.amazonaws.com/   
?Action=CreateQueue
&DefaultVisibilityTimeout=40
&QueueName=MyQueue
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Queue names and queue URLs are case\-sensitive\.  
The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

## Making a GET Request<a name="structure-get-request"></a>

An Amazon SQS `GET` request is structured as a URL which consists of the following:

+ **Endpoint** – The resource that the request is acting on \(the [queue name and URL](sqs-general-identifiers.md#queue-name-url)\), for example: `https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue`

+ **Action** – The [API action](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_Operations.html) that you want to perform on the endpoint\. A question mark \(`?`\) separates the endpoint from the action, for example: `?Action=SendMessage&MessageBody=Your%20Message%20Text`

+ **Parameters** – Any request parameters—each parameter is separated by an ampersand \(`&`\), for example: `&Version=2012-11-05&AUTHPARAMS` 

The following is an example of a `GET` request that sends a message to an Amazon SQS queue\.

```
https://sqs.us-east-2.amazonaws.com/123456789012/MyQueue
?Action=SendMessage&MessageBody=Your%20message%20text
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Queue names and queue URLs are case\-sensitive\.  
Because `GET` requests are URLs, you must URL\-encode all parameter values\. Because spaces aren't allowed in URLs, each space is URL\-encoded as `%20`\. \(The rest of the example isn't URL\-encoded to make it easier to read\.\)

## Making a POST Request<a name="structure-post-request"></a>

An Amazon SQS `POST` requests send query parameters as a form in the body of an HTTP request\.

The following is an example of a HTTP header with `Content-Type` set to `application/x-www-form-urlencoded`\. 

```
POST /MyQueue HTTP/1.1
Host: sqs.us-east-2.amazonaws.com
Content-Type: application/x-www-form-urlencoded
```

The header is followed by a `[form\-urlencoded](https://www.w3.org/MarkUp/html-spec/html-spec_8.html#SEC8.2)` `POST` request that sends a message to an Amazon SQS queue\. Each parameter is separated by an ampersand \(`&`\)\.

```
Action=SendMessage
&MessageBody=Your+Message+Text
&Expires=2020-10-15T12%3A00%3A00Z
&Version=2012-11-05
&AUTHPARAMS
```

**Note**  
Only the `Content-Type` HTTP header is required\. The `AUTHPARAMS` is the same as for the `GET` request\.  
Your HTTP client might add other items to the HTTP request, according to the client's HTTP version\.

## Authenticating Requests<a name="sqs-api-request-authentication"></a>

Authentication is the process of identifying and verifying the party that sends a request\. During the first stage of authentication, AWS verifies the identity of the producer and whether the producer is [registered to use AWS](https://aws.amazon.com/) \(for more information, see [Step 1: Create an AWS Account](sqs-setting-up.md#sqs-creating-aws-account) and [Step 2: Create an IAM User](sqs-setting-up.md#sqs-creating-iam-user)\)\. Next, AWS abides by the following procedure:

1. The producer \(sender\) obtains the necessary credential\.

1. The producer sends a request and the credential to the consumer \(receiver\)\.

1. The consumer uses the credential to verify whether the producer sent the request\.

1. One of the following happens:

   + If authentication succeeds, the consumer processes the request\.

   + If authentication fails, the consumer rejects the request and returns an error\.

### Basic Authentication Process with HMAC\-SHA<a name="sqs-api-basic-authentication-process"></a>

When you access Amazon SQS using the Query API, you must provide the following items to authenticate your request:

+ The **AWS Access Key ID** that identifies your AWS account, which AWS uses to look up your Secret Access Key\.

+ The **HMAC\-SHA request signature**, calculated using your Secret Access Key \(a shared secret known only to you and AWS—for more information, see [RFC2104](http://www.faqs.org/rfcs/rfc2104.html)\)\. The [AWS SDK](https://aws.amazon.com/code/) handles the signing process; however, if you submit a query request over HTTP or HTTPS, you must include a signature in every query request\.

  1. Derive a Signature Version 4 Signing Key\. For more information, see [Deriving the Signing Key with Java](http://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html#signature-v4-examples-java)\.
**Note**  
Amazon SQS supports Signature Version 4, which provides improved SHA256\-based security and performance over previous versions\. When you create new applications that use Amazon SQS, use Signature Version 4\.

  1. Base64\-encode the request signature\. The following sample Java code does this:

     ```
     package amazon.webservices.common;
     
     // Define common routines for encoding data in AWS requests.
     public class Encoding {
     
         /* Perform base64 encoding of input bytes.
          * rawData is the array of bytes to be encoded.
          * return is the base64-encoded string representation of rawData.
          */
         public static String EncodeBase64(byte[] rawData) {
             return Base64.encodeBytes(rawData);
         }
     }
     ```

+ The **timestamp \(or expiration\)** of the request\. The timestamp that you use in the request must be a `dateTime` object, with [the complete date, including hours, minutes, and seconds](http://www.w3.org/TR/xmlschema-2/#dateTime)\. For example: `2007-01-31T23:59:59Z` Although this isn't required, we recommend providing the object using the Coordinated Universal Time \(Greenwich Mean Time\) time zone\.
**Note**  
Make sure that your server time is set correctly\. If you specify a timestamp \(rather than an expiration\), the request automatically expires 15 minutes after the specified time \(AWS doesn't process requests with timestamps more than 15 minutes earlier than the current time on AWS servers\)\.  
If you use \.NET, you must not send overly specific timestamps \(because of different interpretations of how extra time precision should be dropped\)\. In this case, you should manually construct `dateTime` objects with precision of no more than one millisecond\.

#### Part 1: The Request from the User<a name="sqs-authentication-user"></a>

The following is the process you must follow to authenticate AWS requests using an HMAC\-SHA request signature\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-basic-authentication-process-hmac-user.png)

1. Construct a request to AWS\. 

1. Calculate a keyed\-hash message authentication code \(HMAC\-SHA\) signature using your Secret Access Key\.

1. Include the signature and your Access Key ID in the request, and then send the request to AWS\.

#### Part 2: The Response from AWS<a name="sqs-authentication-aws"></a>

AWS begins the following process in response\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-basic-authentication-process-hmac-aws.png)

1. AWS uses the Access Key ID to look up your Secret Access Key\.

1. AWS generates a signature from the request data and the Secret Access Key, using the same algorithm that you used to calculate the signature you sent in the request\.

1. One of the following happens:

   + If the signature that AWS generates matches the one you send in the request, AWS considers the request to be authentic\.

   + If the comparison fails, the request is discarded, and AWS returns an error\. 

## Interpreting Responses<a name="sqs-api-responses"></a>

In response to an action request, Amazon SQS returns an XML data structure that contains the results of the request\. For more information, see the individual API actions in the *[Amazon Simple Queue Service API Reference](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/)*\.

### Successful Response Structure<a name="sqs-api-successful-response-structure"></a>

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

### Error Response Structure<a name="sqs-api-error-response-structure"></a>

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