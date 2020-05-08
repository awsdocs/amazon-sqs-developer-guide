# Using temporary security credentials with Amazon SQS<a name="sqs-using-temporary-security-credentials"></a>

In addition to creating IAM users with their own security credentials, IAM also allows you to grant temporary security credentials to any user, allowing the user to access your AWS services and resources\. You can manage users who have AWS accounts \(IAM users\)\. You can also manage users for your system who don't have AWS accounts \(federated users\)\. In addition, applications that you create to access your AWS resources can also be considered to be "users\."

You can use these temporary security credentials to make requests to Amazon SQS\. The API libraries compute the necessary signature value using those credentials to authenticate your request\. If you send requests using expired credentials, Amazon SQS denies the request\.

**Note**  
You can't set a policy based on temporary credentials\.

## Prerequisites<a name="temporary-security-credentials-prerequisites"></a>

1. Use IAM to create temporary security credentials:
   + Security token
   + Access Key ID
   + Secret Access Key

1. Prepare your string to sign with the temporary Access Key ID and the security token\.

1. Use the temporary Secret Access Key instead of your own Secret Access Key to sign your Query API request\.

**Note**  
When you submit the signed Query API request, use the temporary Access Key ID instead of your own Access Key ID and to include the security token\. For more information about IAM support for temporary security credentials, see [Granting Temporary Access to Your AWS Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/TokenBasedAuth.html) in the *IAM User Guide*\. 

## To call an Amazon SQS Query API action using temporary security credentials<a name="temporary-security-credentials-query-api"></a>

1. Request a temporary security token using AWS Identity and Access Management\. For more information, see [Creating Temporary Security Credentials to Enable Access for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/CreatingSessionTokens.html) in the *IAM User Guide*\.

   IAM returns a security token, an Access Key ID, and a Secret Access Key\.

1. Prepare your query using the temporary Access Key ID instead of your own Access Key ID and include the security token\. Sign your request using the temporary Secret Access Key instead of your own\.

1. Submit your signed query string with the temporary Access Key ID and the security token\.

   The following example demonstrates how to use temporary security credentials to authenticate an Amazon SQS request\. The structure of *`AUTHPARAMS`* depends on the signature of the API request\. For more information, see [Signing AWS API Requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *Amazon Web Services General Reference*\.

   ```
   https://sqs.us-east-2.amazonaws.com/
   ?Action=CreateQueue
   &DefaultVisibilityTimeout=40
   &QueueName=MyQueue
   &Attribute.1.Name=VisibilityTimeout
   &Attribute.1.Value=40
   &Expires=2020-12-18T22%3A52%3A43PST
   &SecurityToken=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   &AWSAccessKeyId=AKIAIOSFODNN7EXAMPLE
   &Version=2012-11-05
   &AUTHPARAMS
   ```

   The following example uses temporary security credentials to send two messages using the `SendMessageBatch` action\.

   ```
   https://sqs.us-east-2.amazonaws.com/
   ?Action=SendMessageBatch
   &SendMessageBatchRequestEntry.1.Id=test_msg_001
   &SendMessageBatchRequestEntry.1.MessageBody=test%20message%20body%201
   &SendMessageBatchRequestEntry.2.Id=test_msg_002
   &SendMessageBatchRequestEntry.2.MessageBody=test%20message%20body%202
   &SendMessageBatchRequestEntry.2.DelaySeconds=60
   &Expires=2020-12-18T22%3A52%3A43PST
   &SecurityToken=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
   &AWSAccessKeyId=AKIAI44QH8DHBEXAMPLE
   &Version=2012-11-05
   &AUTHPARAMS
   ```