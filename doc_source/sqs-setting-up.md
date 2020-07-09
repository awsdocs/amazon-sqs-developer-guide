# Setting up Amazon SQS<a name="sqs-setting-up"></a>

**Topics**
+ [Step 1: Create an AWS account](#sqs-creating-aws-account)
+ [Step 2: Create an IAM user](#sqs-creating-iam-user)
+ [Step 3: Get your access key ID and secret access key](#sqs-getting-access-key-id-secret-access-key)
+ [Step 4: Get ready to use the example code](#sqs-get-ready-to-use-example-code)
+ [Next steps](#sqs-next-steps-setting-up)

Before you can use Amazon SQS for the first time, you must complete the following steps\.

## Step 1: Create an AWS account<a name="sqs-creating-aws-account"></a>

To access any AWS service, you first need to create an [ AWS account](https://aws.amazon.com/), an Amazon\.com account that can use AWS products\. You can use your AWS account to view your activity and usage reports and to manage authentication and access\.

To avoid using your AWS account root user for Amazon SQS actions, it is a best practice to create an IAM user for each person who needs administrative access to Amazon SQS\.

**To set up a new account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Step 2: Create an IAM user<a name="sqs-creating-iam-user"></a>

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM Entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

## Step 3: Get your access key ID and secret access key<a name="sqs-getting-access-key-id-secret-access-key"></a>

To use Amazon SQS actions \(for example, using Java or through the AWS Command Line Interface\), you need an access key ID and a secret access key\.

**Note**  
The access key ID and secret access key are specific to AWS Identity and Access Management\. Don't confuse them with credentials for other AWS services, such as Amazon EC2 key pairs\.

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. As a best practice, do not use the AWS account root user access keys for any task where it's not required\. Instead, [create a new administrator IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with access keys for yourself\.

The only time that you can view or download the secret access key is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions Required to Access IAM Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

**To create access keys for an IAM user**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose the name of the user whose access keys you want to create, and then choose the **Security credentials** tab\.

1. In the **Access keys** section, choose **Create access key**\.

1. To view the new access key pair, choose **Show**\. You will not have access to the secret access key again after this dialog box closes\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

   Keep the keys confidential in order to protect your AWS account and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

1. After you download the `.csv` file, choose **Close**\. When you create an access key, the key pair is active by default, and you can use the pair right away\.

**Related topics**
+ [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS Security Credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Step 4: Get ready to use the example code<a name="sqs-get-ready-to-use-example-code"></a>

This guide includes examples that use the AWS SDK for Java\. To run the example code, follow the set\-up instructions in [Getting Started with AWS SDK for Java 2\.0](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/)\. 

You can develop AWS applications in other programming languages, such as Go, JavaScript, Python and Ruby\. For more information, see [Tools for developing and managing applications on AWS](https://aws.amazon.com/tools/#sdk)\.

**Note**  
You can explore Amazon SQS without writing code with tools such as the AWS Command Line Interface \(AWS CLI\) or Windows PowerShell\. You can find AWS CLI examples in the [Amazon SQS section](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html) of the *AWS CLI Command Reference*\. You can find Windows PowerShell examples in the Amazon Simple Queue Service section of the *[AWS Tools for PowerShell Cmdlet Reference](https://docs.aws.amazon.com/powershell/latest/reference/)*\.

## Next steps<a name="sqs-next-steps-setting-up"></a>

You are now ready for [Getting started](sqs-getting-started.md) with managing Amazon SQS queues and messages using the AWS Management Console\. 