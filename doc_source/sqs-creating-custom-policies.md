# Creating Custom Policies Using the Amazon SQS Access Policy Language<a name="sqs-creating-custom-policies"></a>

If you want to allow Amazon SQS access based only on an AWS account ID and basic permissions \(such as for [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) or [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)\), you don't need to write your own policies\. You can just use the Amazon SQS [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action\.

If you want to explicitly deny or allow access based on more specific conditions \(such as the time the request comes in or the IP address of the requester\), you need to write your own Amazon SQS policies and upload them to the AWS system using the Amazon SQS `SetQueueAttributes` action\.

## Key Concepts<a name="sqs-creating-custom-policies-key-concepts"></a>

To write your own policies, you must be familiar with [JSON](http://json.org/) and a number of key concepts\.

**allow**  <a name="allow"></a>
The result of a [statement](#statement) that has [effect](#effect) set to `allow`\.

**action**  <a name="action"></a>
The activity that the [principal](#principal) has permission to perform, typically a request to AWS\.

**default\-deny**  <a name="default-deny"></a>
The result of a [statement](#statement) that that has no [allow](#allow) or [explicit deny](#explicit-deny) settings\.

**condition**  <a name="condition"></a>
Any restriction or detail about a [permission](#permission)\. Typical conditions are related to date and time and IP addresses\.

**effect**  <a name="effect"></a>
The result that you want the [statement](#statement) of a [policy](#policy) to return at evaluation time\. You specify the `deny` or `allow` value when you write the policy statement\. There can be three possible results at policy evaluation time: [default-deny](#default-deny), [allow](#allow), and [explicit deny](#explicit-deny)\.

**explicit deny**  <a name="explicit-deny"></a>
The result of a [statement](#statement) that has [effect](#effect) set to `deny`\.

**evaluation**  <a name="evaluation"></a>
The process that Amazon SQS uses to determine whether an incoming request should be denied or allowed based on a [policy](#policy)\.

**issuer**  <a name="issuer"></a>
The user who writes a [policy](#policy) to grant permissions to a resource\. The issuer, by definition is always the resource owner\. AWS doesn't permit Amazon SQS users to create policies for resources they don't own\.

**key**  <a name="key"></a>
The specific characteristic that is the basis for access restriction\.

**permission**  <a name="permission"></a>
The concept of allowing or disallowing access to a resource using a [condition](#condition) and a [key](#key)\.

**policy**  <a name="policy"></a>
The document that acts as a container for one or more [statements](#statement)\.  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Statement_and_Policy.png)
Amazon SQS uses the policy to determine whether to grant access to a user for a resource\.

**principal**  <a name="principal"></a>
The user who receives [permission](#permission) in the [policy](#policy)\.

**resource**  <a name="resource"></a>
The object that the [principal](#principal) requests access to\.

**statement**  <a name="statement"></a>
The formal description of a single permission, written in the access policy language as part of a broader [policy](#policy) document\.

**requester**  <a name="requester"></a>
The user who sends a request for access to a [resource](#resource)\.

## Architecture<a name="sqs-creating-custom-policies-architecture"></a>

The following figure and table describe the access control for your Amazon SQS resources\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Arch_Overview.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) You, the resource owner\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) Your resources contained within the AWS service \(for example, Amazon SQS queues\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) Your policies\. It is a good practice to have one policy per resource The AWS service itself provides an API you use to upload and manage your policies\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-4-red.png) Requesters and their incoming requests to the AWS service\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-5-red.png) The access policy language evaluation code\. This is the set of code within the AWS service that evaluates incoming requests against the applicable policies and determines whether the requester is allowed access to the resource\.

## Process Workflow<a name="sqs-creating-custom-policies-process-workflow"></a>

The following figure and table describe the general workflow of access control with the Amazon SQS access policy language\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Basic_Flow.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) You write an Amazon SQS policy for your queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) You upload your policy to AWS\. The AWS service provides an API that you use to upload your policies\. For example, you use the Amazon SQS `SetQueueAttributes` action to upload a policy for a particular Amazon SQS queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) Someone sends a request to use your Amazon SQS queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-4-red.png) Amazon SQS examines all available Amazon SQS policies and determines which ones are applicable\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-5-red.png) Amazon SQS evaluates the policies and determines whether the requester is allowed to use your queue\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-6-red.png) Based on the policy evaluation result, Amazon SQS either returns an `Access denied` error to the requester or continues to process the request\.

## Evaluation Logic<a name="sqs-creating-custom-policies-evaluation-logic"></a>

At evaluation time, Amazon SQS determines whether a request from someone other than the resource owner should be allowed or denied\. The evaluation logic follows several basic rules:

+ By default, all requests to use your resource coming from anyone but you are denied\.

+ An [allow](#allow) overrides any [default-deny](#default-deny)\.

+ An [explicit deny](#explicit-deny) overrides any [allow](#allow)\.

+ The order in which the policies are evaluated isn't important\.

The following figure and table describe in detail how Amazon SQS evaluates decisions about access permissions\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Evaluation_Flow.png)

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-1-red.png) The decision starts with a [default-deny](#default-deny)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-2-red.png) The enforcement code evaluates all the policies that are applicable to the request \(based on the resource, principal, action, and conditions\)\. The order in which the enforcement code evaluates the policies isn't important\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-3-red.png) The enforcement code looks for an [explicit deny](#explicit-deny) instruction that can apply to the request\. If it finds even one, the enforcement code returns a decision of *deny* and the process finishes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-4-red.png) If no [explicit deny](#explicit-deny) instruction is found, the enforcement code looks for any [allow](#allow) instructions that can apply to the request\. If it finds even one, the enforcement code returns a decision of *allow* and the process finishes \(the service continues to process the request\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/number-5-red.png) If no [allow](#allow) instruction is found, then the final decision is *deny* \(because there is no [explicit deny](#explicit-deny) or [allow](#allow), this is considered a [default-deny](#default-deny)\.

## Relationships Between Explicit and Default Denials<a name="sqs-creating-custom-policies-relationships-between-explicit-default-denials"></a>

If an Amazon SQS policy doesn't directly apply to a request, the request results in a [default-deny](#default-deny)\. For example, if a user requests permission to use Amazon SQS but the only policy that applies to the user can use DynamoDB, the requests results in a [default-deny](#default-deny)\.

If a condition in a statement isn't met, the request results in a [default-deny](#default-deny)\. If all conditions in a statement are met, the request results in either an [allow](#allow) or an [explicit deny](#explicit-deny) based on the value of the [effect](#effect) element of the policy\. Policies don't specify what to do if a condition isn't met, so the default result in this case is a [default-deny](#default-deny)\. For example, you want to prevent requests that come from Antarctica\. You write Policy A1 that allows a request only if it doesn't come from Antarctica\. The following diagram illustrates the Amazon SQS policy\.

![\[Architectural Overview\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-security-custom-policy-allow-request-if-not-from-antarctica.png)

If a user sends a request from the U\.S\., the condition is met \(the request isn't from Antarctica\), and the request results in an [allow](#allow)\. However, if a user sends a request from Antarctica, the condition isn't met and the request defaults to a [default-deny](#default-deny)\. You can change the result to an [explicit deny](#explicit-deny) by writing Policy A2 that explicitly denies a request if it comes from Antarctica\. The following diagram illustrates the policy\.

![\[Architectural Overview\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-security-custom-policy-explicitly-deny-request-if-from-antarctica.png)

If a user sends a request from Antarctica, the condition is met and the request results in an [explicit deny](#explicit-deny)\.

The distinction between a [default-deny](#default-deny) and an [explicit deny](#explicit-deny) is important because an [allow](#allow) can overwrite the former but not the latter\. For example, Policy B allows requests if they arrive on June 1, 2010\. The following diagram compares combining this policy with Policy A1 and Policy A2\.

![\[Overriding\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/sqs-security-custom-policy-compare-allow-request-deny-request-policies-override.png)

In Scenario 1, Policy A1 results in a [default-deny](#default-deny) and Policy B results in an [allow](#allow) because the policy allows requests that come in on June 1, 2010\. The [allow](#allow) from Policy B overrides the [default-deny](#default-deny) from Policy A1, and the request is allowed\.

In Scenario 2, Policy B2 results in an [explicit deny](#explicit-deny) and Policy B results in an [allow](#allow)\. The [explicit deny](#explicit-deny) from Policy A2 overrides the [allow](#allow) from Policy B, and the request is denied\.

## Amazon SQS Access Policy Examples<a name="sqs-creating-custom-policies-access-policy-examples"></a>

The following are examples of typical Amazon SQS access control policies\.

### Example 1: Give Permission to One Account<a name="one-account"></a>

The following example Amazon SQS policy gives AWS account 111122223333 permission to send to and receive from `queue2` owned by AWS account 444455556666\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase1",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"  
   }]
}
```

### Example 2: Give Permission to One or More Accounts<a name="two-accounts"></a>

The following example Amazon SQS policy gives one or more AWS accounts access to queues owned by your account for a specific time period\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying a time restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase2",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333",
            "444455556666"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         }
      }   
   }]
}
```

### Example 3: Give Permission to Requests from Amazon EC2 Instances<a name="requests-from-ec2"></a>

The following example Amazon SQS policy gives access to requests that come from Amazon EC2 instances\. This example builds on the "[Example 2: Give Permission to One or More Accounts](#two-accounts)" example: it restricts access to before June 30, 2009 at 12 noon \(UTC\), it restricts access to the IP range `10.52.176.0/24`\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit specifying an IP address restriction when granting access to a queue\.

```
{   
   "Version": "2012-10-17",
   "Id": "UseCase3",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Allow",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2",
      "Condition": {
         "DateLessThan": {
            "AWS:CurrentTime": "2009-06-30T12:00Z"
         },
         "IpAddress": {
            "AWS:SourceIp": "10.52.176.0/24"
         }
      }   
   }]
}
```

### Example 4: Deny Access to a Specific Account<a name="deny-account"></a>

The following example Amazon SQS policy denies a specific AWS account access to your queue\. This example builds on the "[Example 1: Give Permission to One Account](#one-account)" example: it denies access to the specified AWS account\. It is necessary to write this policy and to upload it to Amazon SQS using the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html) action because the [http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_AddPermission.html) action doesn't permit deny access to a queue \(it allows only granting access to a queue\)\. 

```
{ 
   "Version": "2012-10-17",
   "Id": "UseCase4",
   "Statement" : [{
      "Sid": "1", 
      "Effect": "Deny",           
      "Principal": {
         "AWS": [
            "111122223333"
         ]
      },
      "Action": [
         "sqs:SendMessage",
         "sqs:ReceiveMessage"
      ], 
      "Resource": "arn:aws:sqs:us-east-2:444455556666:queue2"   
   }]
}
```