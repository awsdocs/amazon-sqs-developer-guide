# Amazon SQS Access Policy Language key concepts<a name="sqs-creating-custom-policies-key-concepts"></a>

To write your own policies, you must be familiar with [JSON](http://json.org/) and a number of key concepts\.

**Allow**  <a name="allow"></a>
The result of a [Statement](#statement) that has [Effect](#effect) set to `allow`\.

**Action**  <a name="action"></a>
The activity that the [Principal](#principal) has permission to perform, typically a request to AWS\.

**Default\-deny**  <a name="default-deny"></a>
The result of a [Statement](#statement) that has no [Allow](#allow) or [Explicit-deny](#explicit-deny) settings\.

**Condition**  <a name="condition"></a>
Any restriction or detail about a [Permission](#permission)\. Typical conditions are related to date and time and IP addresses\.

**Effect**  <a name="effect"></a>
The result that you want the [Statement](#statement) of a [Policy](#policy) to return at evaluation time\. You specify the `deny` or `allow` value when you write the policy statement\. There can be three possible results at policy evaluation time: [Default-deny](#default-deny), [Allow](#allow), and [Explicit-deny](#explicit-deny)\.

**Explicit\-deny**  <a name="explicit-deny"></a>
The result of a [Statement](#statement) that has [Effect](#effect) set to `deny`\.

**Evaluation**  <a name="evaluation"></a>
The process that Amazon SQS uses to determine whether an incoming request should be denied or allowed based on a [Policy](#policy)\.

**Issuer**  <a name="issuer"></a>
The user who writes a [Policy](#policy) to grant permissions to a resource\. The issuer, by definition is always the resource owner\. AWS doesn't permit Amazon SQS users to create policies for resources they don't own\.

**Key**  <a name="key"></a>
The specific characteristic that is the basis for access restriction\.

**Permission**  <a name="permission"></a>
The concept of allowing or disallowing access to a resource using a [Condition](#condition) and a [Key](#key)\.

**Policy**  <a name="policy"></a>
The document that acts as a container for one or more **[statements](#statement)**\.  

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/images/AccessPolicyLanguage_Statement_and_Policy.png)
Amazon SQS uses the policy to determine whether to grant access to a user for a resource\.

**Principal**  <a name="principal"></a>
The user who receives [Permission](#permission) in the [Policy](#policy)\.

**Resource**  <a name="resource"></a>
The object that the [Principal](#principal) requests access to\.

**Statement**  <a name="statement"></a>
The formal description of a single permission, written in the access policy language as part of a broader [Policy](#policy) document\.

**Requester**  <a name="requester"></a>
The user who sends a request for access to a [Resource](#resource)\.