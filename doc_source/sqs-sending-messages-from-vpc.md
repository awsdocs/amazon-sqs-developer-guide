# Tutorial: Sending a message to an Amazon SQS queue from Amazon Virtual Private Cloud<a name="sqs-sending-messages-from-vpc"></a>

In this tutorial, you learn how to send messages to an Amazon SQS queue over a secure, private network\. This network consists of a VPC that contains an Amazon EC2 instance\. The instance connects to Amazon SQS through an *interface VPC endpoint*, allowing you to connect to the Amazon EC2 instance and send messages to the Amazon SQS queue even though the network is disconnected from the public internet\. For more information, see [Amazon Virtual Private Cloud endpoints for Amazon SQS](sqs-internetwork-traffic-privacy.md#sqs-vpc-endpoints)\.

**Important**  
You can use Amazon Virtual Private Cloud only with HTTPS Amazon SQS endpoints\.
When you configure Amazon SQS to send messages from Amazon VPC, you must enable private DNS and specify endpoints in the format `sqs.us-east-2.amazonaws.com`\.
Private DNS doesn't support legacy endpoints such as `queue.amazonaws.com` or `us-east-2.queue.amazonaws.com`\.

**Topics**
+ [Step 1: Create an Amazon EC2 key pair](#create-ec2-key-pair)
+ [Step 2: Create AWS resources](#create-aws-resources)
+ [Step 3: Confirm that your EC2 instance isn't publicly accessible](#confirm-ec2-instance-is-not-publicly-accessible)
+ [Step 4: Create an Amazon VPC endpoint for Amazon SQS](#create-vpc-endpoint-for-sqs)
+ [Step 5: Send a message to your Amazon SQS queue](#sqs-vpc-tutorial-publish)

## Step 1: Create an Amazon EC2 key pair<a name="create-ec2-key-pair"></a>

A *key pair* lets you connect to an Amazon EC2 instance\. It consists of a public key that encrypts your login information and a private key that decrypts it\.

1. Sign in to the [Amazon EC2 console](https://console.aws.amazon.com/ec2/)\.

1. On the navigation menu, under **Network & Security**, choose **Key Pairs**\.

1. Choose **Create Key Pair**\.

1. In the **Create Key Pair** dialog box, for **Key pair name**, enter `SQS-VPCE-Tutorial-Key-Pair`, and then choose **Create**\.

1. Your browser downloads the private key file `SQS-VPCE-Tutorial-Key-Pair.pem` automatically\. 
**Important**  
Save this file in a safe place\. EC2 does not generate a `.pem` file for the same key pair a second time\.

1. To allow an SSH client to connect to your EC2 instance, set the permissions for your private key file so that only your user can have read permissions for it, for example:

   ```
   chmod 400 SQS-VPCE-Tutorial-KeyPair.pem
   ```

## Step 2: Create AWS resources<a name="create-aws-resources"></a>

To set up the necessary infrastructure, you must use an AWS CloudFormation *template*, which is a blueprint for creating a *stack* comprised of AWS resources, such as Amazon EC2 instances and Amazon SQS queues\.

The stack for this tutorial includes the following resources:
+ A VPC and the associated networking resources, including a subnet, a security group, an internet gateway, and a route table
+ An Amazon EC2 instance launched into the VPC subnet
+ An Amazon SQS queue

1. Download the AWS CloudFormation template named [https://github.com/aws-samples/amazon-sqs-samples/blob/master/templates/SQS-VPCE-Tutorial-CloudFormation.yaml](https://github.com/aws-samples/amazon-sqs-samples/blob/master/templates/SQS-VPCE-Tutorial-CloudFormation.yaml) from GitHub\.

1. Sign in to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation/)\.

1. Choose **Create Stack**\.

1. On the **Select Template** page, choose **Upload a template to Amazon S3**, select the `SQS-VPCE-SQS-Tutorial-CloudFormation.yaml` file, and then choose **Next**\.

1. On the **Specify Details** page, do the following:

   1. For **Stack name**, enter `SQS-VPCE-Tutorial-Stack`\.

   1. For **KeyName**, choose **SQS\-VPCE\-Tutorial\-Key\-Pair**\.

   1. Choose **Next**\.

1. On the **Options** page, choose **Next**\.

1. On the **Review** page, in the **Capabilities** section, choose **I acknowledge that AWS CloudFormation might create IAM resources with custom names\.**, and then choose **Create**\.

AWS CloudFormation begins to create the stack and displays the **CREATE\_IN\_PROGRESS** status\. When the process is complete, AWS CloudFormation displays the **CREATE\_COMPLETE** status\.

## Step 3: Confirm that your EC2 instance isn't publicly accessible<a name="confirm-ec2-instance-is-not-publicly-accessible"></a>

Your AWS CloudFormation template launches an EC2 instance named `SQS-VPCE-Tutorial-EC2-Instance` into your VPC\. This EC2 instance doesn't allow outbound traffic and isn't able to send messages to Amazon SQS\. To verify this, you must connect to the instance, try to connect to a public endpoint, and then try to message Amazon SQS\.

1. Sign in to the [Amazon EC2 console](https://console.aws.amazon.com/ec2/)\.

1. On the navigation menu, under **Instances**, choose **Instances**\.

1. Select **SQS\-VPCE\-Tutorial\-EC2Instance**\.

1. Copy the hostname under **Public DNS \(IPv4\)**, for example, **ec2\-203\-0\-113\-0\.us\-west\-2\.compute\.amazonaws\.com**\.

1. From the directory that contains [the key pair that you created earlier](#create-ec2-key-pair), connect to the instance using the following command, for example:

   ```
   ssh -i SQS-VPCE-Tutorial-KeyPair.pem ec2-user@ec2-203-0-113-0.us-east-2.compute.amazonaws.com
   ```

1. Try to connect to any public endpoint, for example:

   ```
   ping amazon.com
   ```

   The connection attempt fails, as expected\.

1. Sign in to the [Amazon SQS console](https://console.aws.amazon.com/sqs/)\.

1. From the list of queues, select the queue created by your AWS CloudFormation template, for example, **VPCE\-SQS\-Tutorial\-Stack\-CFQueue\-1ABCDEFGH2IJK**\.

1. On the **Details** table, copy the URL, for example, **https://sqs\.us\-east\-2\.amazonaws\.com/123456789012/**\.

1. From your EC2 instance, try to publish a message to the queue using the following command, for example:

   ```
   aws sqs send-message --region us-east-2 --endpoint-url https://sqs.us-east-2.amazonaws.com/ --queue-url https://sqs.us-east-2.amazonaws.com/123456789012/ --message-body "Hello from Amazon SQS."
   ```

   The sending attempt fails, as expected\.
**Important**  
Later, when you create a VPC endpoint for Amazon SQS, your sending attempt will succeed\.

## Step 4: Create an Amazon VPC endpoint for Amazon SQS<a name="create-vpc-endpoint-for-sqs"></a>

To connect your VPC to Amazon SQS, you must define an interface VPC endpoint\. After you add the endpoint, you can use the Amazon SQS API from the EC2 instance in your VPC\. This allows you to send messages to a queue within the AWS network without crossing the public internet\.

**Note**  
The EC2 instance still doesn't have access to other AWS services and endpoints on the internet\.

1. Sign in to the [Amazon VPC console](https://console.aws.amazon.com/vpc/)\.

1. On the navigation menu, choose **Endpoints**\.

1. Choose **Create Endpoint**\.

1. On the **Create Endpoint** page, for **Service Name**, choose the service name for Amazon SQS\.
**Note**  
The service names vary based on the current AWS Region\. For example, if you are in US East \(Ohio\), the service name is **com\.amazonaws\.*us\-east\-2*\.sqs**\.

1. For **VPC**, choose **SQS\-VPCE\-Tutorial\-VPC**\.

1. For **Subnets**, choose the subnet whose **Subnet ID** contains **SQS\-VPCE\-Tutorial\-Subnet**\.

1. For **Security group**, choose **Select security groups**, and then choose the security group whose **Group Name** contains **SQS VPCE Tutorial Security Group**\.

1. Choose **Create endpoint**\.

   The interface VPC endpoint is created and its ID is displayed, for example, **vpce\-0ab1cdef2ghi3j456k**\.

1. Choose **Close**\. 

   The Amazon VPC console opens the **Endpoints** page\.

Amazon VPC begins to create the endpoint and displays the **pending** status\. When the process is complete, Amazon VPC displays the **available** status\.

## Step 5: Send a message to your Amazon SQS queue<a name="sqs-vpc-tutorial-publish"></a>

Now that your VPC includes an endpoint for Amazon SQS, you can connect to your EC2 instance and send messages to your queue\.

1. Reconnect to your EC2 instance, for example:

   ```
   ssh -i SQS-VPCE-Tutorial-KeyPair.pem ec2-user@ec2-203-0-113-0.us-east-2.compute.amazonaws.com
   ```

1. Try to publish a message to the queue again using the following command, for example:

   ```
   aws sqs send-message --region us-east-2 --endpoint-url https://sqs.us-east-2.amazonaws.com/ --queue-url https://sqs.us-east-2.amazonaws.com/123456789012/ --message-body "Hello from Amazon SQS."
   ```

   The sending attempt succeeds and the MD5 digest of the message body and the message ID are displayed, for example:

   ```
   {
   	"MD5OfMessageBody": "a1bcd2ef3g45hi678j90klmn12p34qr5",
   	"MessageId": "12345a67-8901-2345-bc67-d890123e45fg"
   }
   ```

For information about receiving and deleting the message from the queue created by your AWS CloudFormation template \(for example, **VPCE\-SQS\-Tutorial\-Stack\-CFQueue\-1ABCDEFGH2IJK**\), see [Receiving and deleting messages \(console\)](sqs-using-receive-delete-message.md)\.

For information about deleting your resources, see the following:
+ [Deleting a VPC Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/delete-vpc-endpoint.html) in the *Amazon VPC User Guide*
+ [Deleting an Amazon SQS queue](sqs-using-delete-queue.md)
+ [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*
+ [Deleting Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#VPC_Deleting) in the *Amazon VPC User Guide*
+ [Deleting a Stack on the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html) in the *AWS CloudFormation User Guide*
+ [Deleting Your Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#delete-key-pair) in the *Amazon EC2 User Guide for Linux Instances*