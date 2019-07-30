# Amazon SQS Access Policy Language Key Concepts<a name="sqs-creating-custom-policies-key-concepts"></a>

To write your own policies, you must be familiar with [JSON](http://json.org/) and a number of key concepts\.

**allow**  <a name="allow"></a>
The result of a [statement](#statement) that has [effect](#effect) set to `allow`\.

**action**  <a name="action"></a>
The activity that the [principal](#principal) has permission to perform, typically a request to AWS\.

**default\-deny**  <a name="default-deny"></a>
The result of a [statement](#statement) that has no [allow](#allow) or [explicit-deny](#explicit-deny) settings\.

**condition**  <a name="condition"></a>
Any restriction or detail about a [permission](#permission)\. Typical conditions are related to date and time and IP addresses\.

**effect**  <a name="effect"></a>
The result that you want the [statement](#statement) of a [policy](#policy) to return at evaluation time\. You specify the `deny` or `allow` value when you write the policy statement\. There can be three possible results at policy evaluation time: [default-deny](#default-deny), [allow](#allow), and [explicit-deny](#explicit-deny)\.

**explicit\-deny**  <a name="explicit-deny"></a>
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
The document that acts as a container for one or more **[statements](#statement)**\.  

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