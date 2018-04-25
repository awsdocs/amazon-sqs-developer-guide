# Setting Up Amazon SQS<a name="sqs-setting-up"></a>

Before you can use Amazon SQS for the first time, you must complete the following steps\.

## Step 1: Create an AWS Account<a name="sqs-creating-aws-account"></a>

To access any AWS service, you first need to create an [ AWS account](https://aws.amazon.com/), an Amazon\.com account that can use AWS products\. You can use your AWS account to view your activity and usage reports and to manage authentication and access\.

To avoid using your AWS account root user for Amazon SQS actions, it is a best practice to create an IAM user for each person who needs administrative access to Amazon SQS\.

**To set up a new account**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign in to a different account**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

## Step 2: Create an IAM User<a name="sqs-creating-iam-user"></a>

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in as the *[AWS account root user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)* to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](http://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type ** Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to select a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions for user** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, type ** Administrators**\.

1. For **Filter**, choose **Job function**\.

1. In the policy list, select the check box for ** AdministratorAccess**\. Then choose **Create group**\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users, and to give your users access to your AWS account resources\. To learn about using policies to restrict users' permissions to specific AWS resources, go to [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

## Step 3: Get Your Access Key ID and Secret Access Key<a name="sqs-getting-access-key-id-secret-access-key"></a>

To use Amazon SQS actions \(for example, using Java or through the AWS Command Line Interface\), you need an access key ID and a secret access key\.

**Note**  
The access key ID and secret access key are specific to AWS Identity and Access Management\. Don't confuse them with credentials for other AWS services, such as Amazon EC2 key pairs\.

**To get the access key ID and secret access key for an IAM user**

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. We recommend that you use IAM access keys instead of AWS account root user access keys\. IAM lets you securely control access to AWS services and resources in your AWS account\.

The only time that you can view or download the secret access keys is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions Required to Access IAM Resources](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane of the console, choose **Users**\.

1. Choose your IAM user name \(not the check box\)\.

1. Choose the **Security credentials** tab and then choose **Create access key**\.

1. To see the new access key, choose **Show**\. Your credentials will look something like this:

   + Access key ID: AKIAIOSFODNN7EXAMPLE

   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\.

   Keep the keys confidential in order to protect your AWS account, and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

**Related topics**

+ [What Is IAM?](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*

+ [AWS Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Step 4: Get Ready to Use the Example Code<a name="sqs-get-ready-to-use-example-code"></a>

This guide shows how to work with Amazon SQS using the AWS Management Console and using Java\. If you want to use the example code, you must install the [Java Standard Edition Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/) and make some configuration changes to the example code\.

You can write code in other programming languages\. For more information, see the [documentation of the AWS SDKs](https://aws.amazon.com//tools/#sdk)\.

**Note**  
You can explore Amazon SQS without writing code with tools such as the AWS Command Line Interface \(AWS CLI\) or Windows PowerShell\. You can find AWS CLI examples in the [Amazon SQS section](http://docs.aws.amazon.com/cli/latest/reference/sqs/index.html) of the *AWS CLI Command Reference*\. You can find Windows PowerShell examples in the Amazon Simple Queue Service section of the *[AWS Tools for PowerShell Cmdlet Reference](http://docs.aws.amazon.com/powershell/latest/reference/)*\.

## Next Steps<a name="sqs-next-steps-setting-up"></a>

Now that you're prepared for working with Amazon SQS, can [get started](sqs-getting-started.md) with managing Amazon SQS queues and messages using the AWS Management Console\. You can also try the more advanced Amazon SQS [tutorials](sqs-tutorials.md)\.