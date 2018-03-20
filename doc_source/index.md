# Amazon Simple Queue Service Developer Guide

-----
*****Copyright &copy; 2018 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What is Amazon Simple Queue Service?](Welcome.md)
+ [New and Frequently Viewed Amazon SQS Topics](sqs-newly-added-most-frequently-viewed-topics.md)
+ [Setting Up Amazon SQS](sqs-setting-up.md)
+ [Getting Started with Amazon SQS](sqs-getting-started.md)
+ [Amazon SQS Tutorials](sqs-tutorials.md)
   + [Tutorial: Creating an Amazon SQS Queue](sqs-create-queue.md)
   + [Tutorial: Creating an Amazon SQS Queue with Server-Side Encryption](sqs-create-queue-sse.md)
   + [Tutorial: Configuring Server-Side Encryption (SSE) for an Existing Amazon SQS Queue](sqs-configure-sse-existing-queue.md)
   + [Tutorial: Listing All Amazon SQS Queues in a Region](sqs-list-all-queues.md)
   + [Tutorial: Adding Permissions to an Amazon SQS Queue](sqs-add-permissions.md)
   + [Tutorial: Sending a Message to an Amazon SQS Queue](sqs-send-message.md)
   + [Tutorial: Receiving and Deleting a Message from an Amazon SQS Queue](sqs-receive-delete-message.md)
   + [Tutorial: Configuring an Amazon SQS Dead-Letter Queue](sqs-configure-dead-letter-queue.md)
   + [Tutorial: Purging Messages from an Amazon SQS Queue](sqs-purge-queue.md)
   + [Tutorial: Deleting an Amazon SQS Queue](sqs-delete-queue.md)
   + [Tutorial: Subscribing an Amazon SQS Queue to an Amazon SNS Topic](sqs-subscribe-queue-sns-topic.md)
   + [Tutorial: Adding, Updating, and Removing Cost Allocation Tags for an Amazon SQS Queue](sqs-add-update-remove-tag-queue.md)
+ [How Amazon SQS Queues Work](sqs-how-it-works.md)
   + [Basic Prerequisites](sqs-basic-prerequisites.md)
   + [Standard Queues](standard-queues.md)
   + [FIFO (First-In-First-Out) Queues](FIFO-queues.md)
   + [Queue and Message Identifiers](sqs-queue-message-identifiers.md)
   + [Resources Required to Process Messages](sqs-resources-required-process-messages.md)
   + [Visibility Timeout](sqs-visibility-timeout.md)
   + [Using Amazon SQS Dead-Letter Queues](sqs-dead-letter-queues.md)
   + [Message Lifecycle](sqs-message-lifecycle.md)
   + [Tagging Your Amazon SQS Queues](sqs-queue-tags.md)
   + [Using Amazon SQS Message Attributes](sqs-message-attributes.md)
   + [Amazon SQS Long Polling](sqs-long-polling.md)
   + [Amazon SQS Delay Queues](sqs-delay-queues.md)
   + [Amazon SQS Message Timers](sqs-message-timers.md)
   + [Managing Large Amazon SQS Messages Using Amazon S3](sqs-s3-messages.md)
   + [Using JMS with Amazon SQS](sqs-java-message-service-jms-client.md)
      + [Prerequisites](prerequisites.md)
      + [Getting Started with the Amazon SQS Java Messaging Library](getting-started.md)
      + [Using the Amazon SQS Java Message Service (JMS) Client with Other Amazon SQS Clients](sqs-jms-client-with-sqs-clients.md)
      + [Working Java Example for Using JMS with Amazon SQS Standard Queues](code-examples.md)
      + [Supported JMS 1.1 Implementations](supported-implementations.md)
+ [Best Practices for Amazon SQS](sqs-best-practices.md)
   + [Recommendations for Standard and FIFO (First-In-First-Out) Queues](sqs-standard-fifo-queue-best-practices.md)
   + [Additional Recommendations for FIFO Queues](sqs-additional-fifo-queue-recommendations.md)
+ [Amazon SQS Limits](sqs-limits.md)
+ [Monitoring and Logging Amazon SQS Queues](sqs-monitoring-logging.md)
   + [Monitoring Amazon SQS using CloudWatch](sqs-monitoring-using-cloudwatch.md)
      + [Access CloudWatch Metrics for Amazon SQS](sqs-access-metrics.md)
      + [Setting CloudWatch Alarms for Amazon SQS Metrics](set-cloudwatch-alarms-for-metrics.md)
      + [Available CloudWatch Metrics for Amazon SQS](sqs-available-cloudwatch-metrics.md)
   + [Logging Amazon SQS API Actions Using AWS CloudTrail](logging-using-cloudtrail.md)
+ [Amazon SQS Security](sqs-security.md)
   + [Authentication and Access Control for Amazon SQS](sqs-authentication-and-access-control.md)
      + [Overview of Managing Access Permissions to Your Amazon Simple Queue Service Resource](sqs-overview-of-managing-access.md)
      + [Using Identity-Based Policies (IAM) Policies for Amazon SQS](sqs-using-identity-based-policies.md)
      + [Creating Custom Policies Using the Amazon SQS Access Policy Language](sqs-creating-custom-policies.md)
      + [Using Temporary Security Credentials](sqs-using-temporary-security-credentials.md)
      + [Amazon SQS API Permissions: Actions and Resource Reference](sqs-api-permissions-reference.md)
   + [Protecting Data Using Server-Side Encryption (SSE) and AWS KMS](sqs-server-side-encryption.md)
+ [Working with Amazon SQS APIs](sqs-working-with-apis.md)
   + [Making Query API Requests](sqs-making-api-requests.md)
   + [Amazon SQS Batch API Actions](sqs-batch-api-actions.md)
+ [Related Amazon SQS Resources](related-resources.md)
+ [Amazon SQS Release Notes](sqs-release-notes.md)
   + [Amazon SQS Document History](sqs-document-history.md)
+ [AWS Glossary](glossary.md)