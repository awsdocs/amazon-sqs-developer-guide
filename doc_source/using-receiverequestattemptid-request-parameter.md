# Using the Amazon SQS Receive Request Attempt ID<a name="using-receiverequestattemptid-request-parameter"></a>

The receive request attempt ID is the large, non\-consecutive number that Amazon SQS assigns to each message\.

During a long\-lasting network outage that causes connectivity issues between your SDK and Amazon SQS, it's a best practice to provide the receive request attempt ID and to retry with the same receive request attempt ID if the SDK operation fails\.