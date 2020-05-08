# Using the Amazon SQS receive request attempt ID<a name="using-receiverequestattemptid-request-parameter"></a>

The receive request attempt ID is the token used for deduplication of `ReceiveMessage` calls\.

During a long\-lasting network outage that causes connectivity issues between your SDK and Amazon SQS, it's a best practice to provide the receive request attempt ID and to retry with the same receive request attempt ID if the SDK operation fails\.