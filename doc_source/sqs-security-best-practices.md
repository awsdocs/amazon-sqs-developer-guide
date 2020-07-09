# Amazon SQS security best practices<a name="sqs-security-best-practices"></a>

AWS provides many security features for Amazon SQS, which you should review in the context of your own security policy\.

**Note**  
The specific implementation guidance provided is for common use cases and implementations\. We suggest that you view these best practices in the context of your specific use case, architecture, and threat model\.

## Preventative best practices<a name="preventative-best-practices"></a>

The following are preventative security best practices for Amazon SQS\.

**Topics**
+ [Ensure queues aren't publicly accessible](#ensure-queues-not-publicly-accessible)
+ [Implement least\-privilege access](#implement-least-privilege-access)
+ [Use IAM roles for applications and AWS services which require Amazon SQS access](#use-iam-roles-for-applications-aws-services-which-require-access)
+ [Implement server\-side encryption](#implement-server-side-encryption)
+ [Enforce encryption of data in transit](#enforce-encryption-data-in-transit)
+ [Consider using VPC endpoints to access Amazon SQS](#consider-using-vpc-endpoints-access-sqs)

### Ensure queues aren't publicly accessible<a name="ensure-queues-not-publicly-accessible"></a>

Unless you explicitly require anyone on the internet to be able to read or write to your Amazon SQS queue, you should ensure that your queue isn't publicly accessible \(accessible by everyone in the world or by any authenticated AWS user\)\.
+ Avoid creating policies with `Principal` set to `""`\.
+ Avoid using a wildcard \(`*`\)\. Instead, name a specific user or users\.

### Implement least\-privilege access<a name="implement-least-privilege-access"></a>

When you grant permissions, you decide who receives them, which queues the permissions are for, and specific API actions that you want to allow for these queues\. Implementing least privilege is important to reducing security risks and reducing the effect of errors or malicious intent\.

Follow the standard security advice of granting least privilege\. That is, grant only the permissions required to perform a specific task\. You can implement this using a combination of security policies\.

Amazon SQS uses the producer\-consumer model, requiring three types of user account access:
+ **Administrators** – Access to creating, modifying, and deleting queues\. Administrators also control queue policies\.
+ **Producers** – Access to sending messages to queues\.
+ **Consumers** – Access to receiving and deleting messages from queues\.

For more information, see the following sections:
+ [Identity and access management in Amazon SQS](sqs-authentication-and-access-control.md)
+ [Amazon SQS API permissions: Actions and resource reference](sqs-api-permissions-reference.md)
+ [Using custom policies with the Amazon SQS Access Policy Language](sqs-creating-custom-policies.md)

### Use IAM roles for applications and AWS services which require Amazon SQS access<a name="use-iam-roles-for-applications-aws-services-which-require-access"></a>

For applications or AWS services such as Amazon EC2 to access Amazon SQS queues, they must use valid AWS credentials in their AWS API requests\. Because these credentials aren't rotated automatically, you shouldn't store AWS credentials directly in the application or EC2 instance\.

You should use an IAM role to manage temporary credentials for applications or services that need to access Amazon SQS\. When you use a role, you don't have to distribute long\-term credentials \(such as a user name, password, and access keys\) to an EC2 instance or AWS service such as AWS Lambda\. Instead, the role supplies temporary permissions that applications can use when they make calls to other AWS resources\.

For more information, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Common Scenarios for Roles: Users, Applications, and Services](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios.html) in the *IAM User Guide*\.

### Implement server\-side encryption<a name="implement-server-side-encryption"></a>

To mitigate data leakage issues, use encryption at rest to encrypt your messages using a key stored in a different location from the location that stores your messages\. Server\-side encryption \(SSE\) provides data encryption at rest\. Amazon SQS encrypts your data at the message level when it stores it, and decrypts the messages for you when you access them\. SSE uses keys managed in AWS Key Management Service\. As long as you authenticate your request and have access permissions, there is no difference between accessing encrypted and unencrypted queues\.

For more information, see [Encryption at rest](sqs-server-side-encryption.md) and [Key management](sqs-key-management.md)\.

### Enforce encryption of data in transit<a name="enforce-encryption-data-in-transit"></a>

Without HTTPS \(TLS\), a network\-based attacker can eavesdrop on network traffic or manipulate it, using an attack such as man\-in\-the\-middle\. Allow only encrypted connections over HTTPS \(TLS\) using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Boolean](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Boolean) condition in the queue policy to force requests to use SSL\. 

### Consider using VPC endpoints to access Amazon SQS<a name="consider-using-vpc-endpoints-access-sqs"></a>

If you have queues that you must be able to interact with but which must absolutely not be exposed to the internet, use VPC endpoints to queue access to only the hosts within a particular VPC\. You can use queue policies to control access to queues from specific Amazon VPC endpoints or from specific VPCs\.

Amazon SQS VPC endpoints provide two ways to control access to your messages:
+ You can control the requests, users, or groups that are allowed through a specific VPC endpoint\.
+ You can control which VPCs or VPC endpoints have access to your queue using a queue policy\.

For more information, see [Amazon Virtual Private Cloud endpoints for Amazon SQS](sqs-internetwork-traffic-privacy.md#sqs-vpc-endpoints) and [Creating an Amazon VPC endpoint policy for Amazon SQS](sqs-internetwork-traffic-privacy.md#sqs-vpc-endpoint-policy)\.