# Authenticating requests<a name="sqs-api-request-authentication"></a>

Authentication is the process of identifying and verifying the party that sends a request\. During the first stage of authentication, AWS verifies the identity of the producer and whether the producer is [registered to use AWS](https://aws.amazon.com/) \(for more information, see [Step 1: Create an AWS account](sqs-setting-up.md#sqs-creating-aws-account) and [Step 2: Create an IAM user](sqs-setting-up.md#sqs-creating-iam-user)\)\. Next, AWS abides by the following procedure:

1. The producer \(sender\) obtains the necessary credential\.

1. The producer sends a request and the credential to the consumer \(receiver\)\.

1. The consumer uses the credential to verify whether the producer sent the request\.

1. One of the following happens:
   + If authentication succeeds, the consumer processes the request\.
   + If authentication fails, the consumer rejects the request and returns an error\.

**Topics**
+ [Basic authentication process with HMAC\-SHA](#sqs-api-basic-authentication-process)
+ [Part 1: The request from the user](#sqs-authentication-user)
+ [Part 2: The response from AWS](#sqs-authentication-aws)

## Basic authentication process with HMAC\-SHA<a name="sqs-api-basic-authentication-process"></a>

When you access Amazon SQS using the Query API, you must provide the following items to authenticate your request:
+ The **AWS Access Key ID** that identifies your AWS account, which AWS uses to look up your Secret Access Key\.
+ The **HMAC\-SHA request signature**, calculated using your Secret Access Key \(a shared secret known only to you and AWS—for more information, see [RFC2104](http://www.faqs.org/rfcs/rfc2104.html)\)\. The [AWS SDK](https://aws.amazon.com/code/) handles the signing process; however, if you submit a query request over HTTP or HTTPS, you must include a signature in every query request\.

  1. Derive a Signature Version 4 Signing Key\. For more information, see [Deriving the Signing Key with Java](https://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html#signature-v4-examples-java)\.
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

## Part 1: The request from the user<a name="sqs-authentication-user"></a>

The following is the process you must follow to authenticate AWS requests using an HMAC\-SHA request signature\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-basic-authentication-process-hmac-user.png)

1. Construct a request to AWS\. 

1. Calculate a keyed\-hash message authentication code \(HMAC\-SHA\) signature using your Secret Access Key\.

1. Include the signature and your Access Key ID in the request, and then send the request to AWS\.

## Part 2: The response from AWS<a name="sqs-authentication-aws"></a>

AWS begins the following process in response\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-basic-authentication-process-hmac-aws.png)

1. AWS uses the Access Key ID to look up your Secret Access Key\.

1. AWS generates a signature from the request data and the Secret Access Key, using the same algorithm that you used to calculate the signature you sent in the request\.

1. One of the following happens:
   + If the signature that AWS generates matches the one you send in the request, AWS considers the request to be authentic\.
   + If the comparison fails, the request is discarded, and AWS returns an error\. 