# aws-certified-developer-associate Cheat sheet

- [aws-certified-developer-associate Cheat sheet](#aws-certified-developer-associate-cheat-sheet)
  - [IAM](#iam)
  - [DynamoDB](#dynamodb)
  - [VPC](#vpc)
  - [AWS Cognito](#aws-cognito)
  - [API Gateway](#api-gateway)
  - [Lambda](#lambda)
  - [Elastic Beanstalk](#elastic-beanstalk)
  - [AWS CodeDeploy](#aws-codedeploy)
  - [Cloud watch](#cloud-watch)
  - [Kinesis](#kinesis)
  - [Cloudformation](#cloudformation)
  - [CodeCommit](#codecommit)
  - [Codedeploy](#codedeploy)
  - [SQS](#sqs)
  - [AppSync](#appsync)
  - [S3](#s3)
  - [KMS](#kms)
  - [ECS](#ecs)
  - [X Ray](#x-ray)
  - [Amazon ElastiCache](#amazon-elasticache)
  - [Misc](#misc)
    - [AWS Config](#aws-config)
    - [Quicksight](#quicksight)
    - [AWS Certificate Manager](#aws-certificate-manager)
    - [SWF](#swf)
    - [Additional Point](#additional-point)

## IAM

- manage users and authorization

  - Cognito user pool -> To change password
  - Identity pool -> To access AWS services

- you use GetSessionToken if you want to use MFA to protect programmatic calls to specific AWS API. The GetSessionToken API returns a set of temporary credentials for an AWS account or IAM user

## DynamoDB

- The maximum item size in DynamoDB is 400 KB.

- Enable DynamoDB Streams and set the value of StreamViewType to NEW_IMAGE. Use **Kinesis Adapter** in the application to consume streams from DynamoDB

- If you enable DynamoDB Streams on a table, you can associate the stream ARN with a Lambda function that you write. Immediately after an item in the table is modified, a new record appears in the table’s stream. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records.

- a global secondary index (GSI) is primarily used if you want to query over the entire table, across all partitions. GSI only supports eventual consistency and not strong consistency.

- Management:
  - Local Secondary Indexes can only be created when you are creating the table, there is no way to add Local Secondary Index to an existing table, also once you create the index you cannot delete it.
  - Global Secondary Indexes can be created when you create the table and added to an existing table, deleting an existing Global Secondary Index is also allowed.

- Partition key of the Local Secondary Index must me the same as the one in a base table.

- Amazon DynamoDB is a fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency at any scale.

- perform ordered data replication between applications by using Amazon DynamoDB Stream

      simplest, decoupled, and reliable method to get near-real time updates from the database

- DynamoDB optionally supports conditional writes for these operations. A conditional write succeeds only if the item attributes meet one or more expected conditions. Otherwise, it returns an error. Conditional writes are helpful in many situations. For example, you might want a PutItem operation to succeed only if there is not already an item with the same primary key. Or you could prevent an UpdateItem operation from modifying an item if one of its attributes has a certain value.

      Conditional writes are helpful in cases where multiple users attempt to modify the same item.

- For Amazon DynamoDB, you can dynamically adjust provisioned throughput capacity in response to actual traffic patterns. This enables a table or a global secondary index to increase its provisioned read and write capacity to handle sudden increases in traffic without throttling.

- RCU

      Step #1 Multiply the value of the provisioned RCU by 4 KB
      = 10 RCU x 4 KB
      = 40

      Step #2 To get the number of strong consistency requests, just divide the result of step 1 to 4 KB
      Divide the Average Item Size by 4 KB since the scenario requires eventual consistency reads:
      = 40 / 4 KB
      = 10 strongly consistent reads requests

      Step #3 To get the number of eventual consistency requests, just divide the result of step 1 to 2 KB
      =40 / 2 KB
      = 20 eventually consistent read requests

- A projection expression is a string that identifies the attributes you want. To retrieve a single attribute, specify its name. For multiple attributes, the names must be comma-separated.

- Set ScanIndexForward to False to reverse the order for Query(Not applicable for Scan operation)

- ReturnConsumedCapacity: To return the number of write capacity units consumed by any of these operations, set the ReturnConsumedCapacity parameter to one of the following:

      TOTAL — returns the total number of write capacity units consumed.

      INDEXES — returns the total number of write capacity units consumed, with subtotals for the table and any secondary indexes that were affected by the operation.

      NONE — no write capacity details are returned. (This is the default.)

- **TransactWriteItems** is a synchronous and idempotent write operation that groups up to 25 write actions in a single all-or-nothing operation.
  - A TransactWriteItems operation differs from a BatchWriteItem operation in that all the actions it contains must be completed successfully, or no changes are made at all. With a BatchWriteItem operation, it is possible that only some of the actions in the batch succeed while the others do not.

## VPC

- default route limit per VPC is 200.
- a subnet can only be associated with one route table at a time.
- it is definitely possible to modify/edit the main route table.

## AWS Cognito

- Use AWS Cognito Identity Pools then enable access to unauthenticated identities.

## API Gateway

- Using stage variables that can be configured, an API deployment stage can interact with different backend endpoints. Users can use API Gateway stage variables to reference a single AWS Lambda function with multiple versions and aliases

- The following are the Gateway response types which are associated with the HTTP 504 error in API Gateway:

      INTEGRATION_FAILURE – The gateway response for an integration failed error. If the response type is unspecified, this response defaults to the DEFAULT_5XX type.

      INTEGRATION_TIMEOUT – The gateway response for an integration timed out error. If the response type is unspecified, this response defaults to the DEFAULT_5XX type.

- For the integration timeout, the range is from 50 milliseconds to 29 seconds for all integration types, including Lambda, Lambda proxy, HTTP, HTTP proxy, and AWS integrations. the underlying Lambda function has been running for more than 29 seconds causing the API Gateway request to time out.

- **The API Gateway automatically enabled throttling in peak times which caused the HTTP 504 errors is incorrect** because a large number of incoming requests will most likely produce an HTTP 502 or 429 error but not a 504 error

- you use GetSessionToken if you want to use MFA to protect programmatic calls to specific AWS API.

- Lambda authorizer

      A Lambda authorizer is an API Gateway feature that uses a Lambda function to control access to your API. When a client makes a request to one of your API’s methods, API Gateway calls your Lambda authorizer, which takes the caller’s identity as input and returns an IAM policy as output.

      There are two types of Lambda authorizers:

      – A token-based Lambda authorizer (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token or SAML.

      – A request parameter-based Lambda authorizer (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables

      For WebSocket APIs, only request parameter-based authorizers are supported.

## Lambda

- when your function returns an error, Lambda stops processing any data in the impacted shard and retries the entire batch of records. These records are continuously retried until they are successfully processed by Lambda or expired by the event source.
  [ref](https://aws.amazon.com/about-aws/whats-new/2019/11/aws-lambda-supports-failure-handling-features-for-kinesis-and-dynamodb-event-sources/?nc1=h_ls)

- Each Lambda function receives 512MB of non-persistent disk space in its own /tmp directory.

- The default timeout for lambda is 3 seconds. The maximum allowed value is 900 seconds(15 min).

- Create an event source mapping to tell Lambda to send records from your stream to a Lambda function. You can create multiple event source mappings to process the same data with multiple Lambda functions, or process items from multiple streams with a single function.

- For Lambda functions that process Kinesis or DynamoDB streams, **the number of shards is the unit of concurrency**. If your stream has 100 active shards, there will be at most 100 Lambda function invocations running concurrently. This is because Lambda processes each shard’s events in sequence.

- A function can use up to 5 layers at a time.

- A runtime is a program that runs a Lambda function’s handler method when the function is invoked. You can include a runtime in your function’s deployment package in the form of an executable file named bootstrap

## Elastic Beanstalk

- configuration files are YAML- or JSON-formatted documents with a .config file extension that you place in a folder named .ebextensions and deploy in your application source bundle-as the healthcheckurl.yaml file should be renamed to healthcheckurl.config file and placed in the .ebextensions directory to be picked up by Elastic Beanstalk.

- You can define periodic tasks in a file named cron.yaml in your source bundle to add jobs to your worker environment’s queue automatically at a regular interval.

## AWS CodeDeploy

- A Developer is trying to deploy a serverless application using AWS CodeDeploy. The application was updated and needs to be redeployed. What file does the Developer need to update to push that change through CodeDeploy?- appspec.yml

## Cloud watch

- Standard resolution, with data having a one-minute granularity
- High resolution, with data at a granularity of one second

## Kinesis

- Typically, when you use the KCL, you should ensure that the number of instances does not exceed the number of shards (except for failure standby purposes). Each shard is processed by exactly one KCL worker and has exactly one corresponding record processor, so you never need multiple instances to process one shard. However, one worker can process any number of shards, so it’s fine if the number of shards exceeds the number of instances.

- You split shards to increase the capacity (and cost) of your stream. You merge shards to reduce the cost (and capacity) of your stream.

- A Kinesis data stream stores records from 24 hours by default, up to 168 hours

- Data will be available within milliseconds to your Amazon Kinesis applications, and those applications will receive data records in the order they were generated.

- You can also use metrics to determine which are your “hot” or “cold” shards, that is, shards that are receiving much more data, or much less data, than expected. You could then selectively split the hot shards to increase capacity for the hash keys that target those shards. Similarly, you could merge cold shards to make better use of their unused capacity.

- Duplication:
  - Producer Retries
    - Applications that need strict guarantees should embed a primary key within the record to remove duplicates later when processing.
  - Consumer Retries
    - checkpoint by specifying Last-Sequence-Num

## Cloudformation

- Cloudformation does not have the rollback feature :  
  - A stack goes into the UPDATE_ROLLBACK_FAILED state when AWS CloudFormation cannot roll back all changes during an update
- Cloudformation doesn’t have the capability to locally build, test, and debug your application like what AWS SAM has.

- SAM uses CodeDeploy and rollbacks are possible

- Changes to a deployment package in Amazon S3 are not detected automatically during stack updates. To update the function code, change the object key or version in the [template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-lambda-function-code.html).

## CodeCommit

- With Git credentials, you generate a static user name and password in IAM. You then use these credentials for HTTPS connections with Git and any third-party tool that supports Git user name and password authentication.
- With SSH connections, you create public and private key files on your local machine that Git and CodeCommit use for SSH authentication. You associate the public key with your IAM user, and you store the private key on your local machine

## Codedeploy

- CodeDeploy provides two deployment type options, in-place deployments and blue/green deployments.
  - In-place deployment
    - Only deployments that use the EC2/On-Premises compute platform can use in-place deployments. (AWS Lambda compute platform deployments cannot use an in-place deployment type.)
  - Blue/green deployment
    - Blue/green on an EC2/On-Premises compute platform
    - Blue/green on an AWS Lambda compute platform.
    - Blue/green on an Amazon ECS compute platform
    - Blue/green deployments through AWS CloudFormation

## SQS

- Polling

      With short polling, if you polled SQS every 10s, you'd only receive a message every 10s, assuming a message is available. e.g. If the message landed in the queue at 15s after the 1st short poll (0s), you'd receive the message at the 3rd short poll at 20s.
      However, if you used long polling, the *connection stays open* to SQS until a message has been found in SQS, or until the timeout (e.g. max 20s) is reached, e.g. If the message landed in the queue at 15s after the 1st LONG poll (0s), the message is returned to the client at 15s. If no message is received, the connection times out at 20s, and a new connection is established.
      (also, any timeout over 0s implies long polling)

- The default visibility timeout for a message is 30 seconds. The maximum is 12 hours.

- Unlike standard queues, FIFO queues don’t introduce duplicate messages. FIFO queues help you avoid sending duplicates to a queue. If you retry the SendMessage action within the 5-minute deduplication interval, Amazon SQS doesn’t introduce any duplicates into the queue.

- Delay queues are similar to visibility timeouts because both features make messages unavailable to consumers for a specific period of time. The difference between the two is that, for delay queues, a message is hidden when it is first added to queue, whereas for visibility timeouts a message is hidden only after it is consumed from the queue.

- To set delay seconds on individual messages, rather than on an entire queue, use **message timers** to allow Amazon SQS to use the message timer's DelaySeconds value instead of the delay queue's **DelaySeconds** value.
  - FIFO queues don't support timers on individual messages.

- Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren’t included in a response). This type of polling is suitable if the new messages that are being added to the SQS queue arrive less frequently.

## AppSync

- AWS AppSync is a fully managed service that makes it easy to develop GraphQL APIs by handling the heavy lifting of securely connecting to data sources like AWS DynamoDB, Lambda, and more.
- AWS AppSync simplifies application development by letting you create a flexible API to securely access, manipulate, and combine data from one or more data sources. AppSync is a managed service that uses GraphQL to make it easy for applications to get exactly the data they need.
- allow multiple users to synchronize and collaborate in real time on shared data

## S3

- The upload size limit for one file is 5GB. But using the multi part upload, this size increases dramatically to 5TB
- To perform a multipart upload with encryption using an AWS KMS key, the requester must have **kms:GenerateDataKey** and **kms:Decrypt** permissions
  
  The kms:GenerateDataKey permissions allow the requester to initiate the upload. With kms:Decrypt permissions, newly uploaded parts can be encrypted with the same key used for previous parts of the same object.

      Note: After all the parts are uploaded successfully, the uploaded parts must be assembled to complete the multipart upload operation. Because the uploaded parts are server-side encrypted using a KMS key, object parts must be decrypted before they can be assembled. For this reason, the requester must have kms:Decrypt permissions for multipart upload requests using server-side encryption with KMS CMKs (SSE-KMS).

## KMS

- Data at rest == KMS, audit== KMS

- Encryption

      A) Server-Side Encryption

      SSE-S3 (AWS-Managed Keys) => When the requirement is to keep the encryption work simple and minimise the maintenance overhead then use SSE-S3.

      SSE-KMS (AWS KMS Keys) => When the requirement is to maintain a security audit trail then use SSE-KMS Keys.

      SSE-C (Customer-Provided Keys) => When end-to-end encryption is not required and the client wants full control of his/her security keys, then use SSE-C.

      B) Client-Side Encryption

      AWS KMS-managed, customer master key => When the requirement is to maintain end-to-end encryption plus a security audit trail, then use AWS KMS Keys.

      Client Managed Master Key => When the requirement is to maintain end-to-end encryption but the client wants full control of his/her security keys, then use Client Managed Master Key.

- GenerateDataKey returns a plaintext copy of the data key along with the copy of the encrypted data key. Use the plain text key to encrypt the data then delete it.
- Envelope encryption is the practice of encrypting plaintext data with a data key, and then encrypting the data key under another key. This top-level plaintext key encryption key is known as the master key

- You can choose to have AWS KMS automatically rotate CMKs every year, provided that those keys were generated within AWS KMS HSMs. Automatic key rotation is not supported for imported keys, asymmetric keys, or keys generated in an AWS CloudHSM cluster using the AWS KMS custom key store feature. If you choose to import keys to AWS KMS or asymmetric keys or use a custom key store, you can manually rotate them by creating a new CMK and mapping an existing key alias from the old CMK to the new CMK.

      Unsupported CMK types: Automatic key rotation is not supported on the following types of CMKs, but you can rotate these CMKs manually.
      - Asymmetric KMS keys
      - KMS keys in custom key stores (backed by AWS CloudHSM clusters)
      - KMS keys that have imported key material

- An error occurred (AccessDenied) when calling the PutObject operation: Access Denied"

      Add permission to the kms:GenerateDataKey action. This permission is required for buckets that use default encryption with a custom AWS KMS key.

- When using server-side encryption with customer-provided encryption keys (SSE-C), you must provide encryption key information using the following request headers:

      x-amz-server-side-encryption-customer-algorithm – This header specifies the encryption algorithm. The header value must be “AES256”.

      x-amz-server-side-encryption-customer-key – This header provides the 256-bit, base64-encoded encryption key for Amazon S3 to use to encrypt or decrypt your data.

      x-amz-server-side-encryption-customer-key-MD5 – This header provides the base64-encoded 128-bit MD5 digest of the encryption key according to RFC 1321. Amazon S3 uses this header for a message integrity check to ensure the encryption key was transmitted without error.

- The value of **AES256** is only applicable for SSE-S3 and SSE-C
- the correct method to upload files in the S3 bucket with SSE-KMS encryption is to include the x-amz-server-side-encryption header with a value of **aws:kms** in your upload request

## ECS

- PortMapping be defined in _Task definition_ when launching containers in Amazon ECS

- ECS task placement strategies

      binpack – Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

      random – Place tasks randomly.

      spread – Place tasks evenly based on the specified value. Accepted values are attribute key-value pairs(such as attribute:ecs.availability-zone to balance tasks across zones), instanceId, or host.

## X Ray

- Use the **GetTraceSummaries** API to get the list of trace IDs and annotations of the application and then retrieve the list of traces using **BatchGetTraces** API.
  - Use filter expressions via the X-Ray console in order to identify and filter out specific data from the trace.

- Annotations are simple key-value pairs that are indexed for use with filter expressions. Use annotations to record data that you want to use to group traces in the console, or when calling the GetTraceSummaries API. X-Ray indexes up to 50 annotations per trace.
- Use annotations to record information on segments or subsegments that you want indexed for search.

- Use X-Ray SDK to generate segment documents with subsegments and send them to the X-Ray daemon, which will buffer them and upload to the X-Ray API in batches- track including all the downstream calls made by the application to AWS resources

- A segment document can be up to 64 kB and contain a whole segment with subsegments, a fragment of a segment that indicates that a request is in progress, or a single subsegment that is sent separately. You can send segment documents directly to X-Ray by using the **PutTraceSegments** API. An alternative is, instead of sending segment documents to the X-Ray API, you can send segments and subsegments to an X-Ray daemon, which will buffer them and upload to the X-Ray API in batches. The X-Ray SDK sends segment documents to the daemon to avoid making calls to AWS directly

- A trace segment is a JSON representation of a request that your application serves. A trace segment records information about the original request, information about the work that your application does locally, and subsegments with information about downstream calls that your application makes to AWS resources, HTTP APIs, and SQL databases.

## Amazon ElastiCache

- Lazy Loading, as its name implies, is a caching strategy that loads data into the cache only when necessary.

## Misc

### AWS Config

- AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources. Config continuously monitors and records your AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations.

### Quicksight

- Quicksight services should you use to build visualizations, get business insights, and continuously analyze your data

### AWS Certificate Manager

- Although you can upload certificates to CloudFront, it doesn’t mean that you can import third-party SSL certificates on it. If you got your certificate from a third-party CA then you have to import the certificate into ACM or upload it to the IAM certificate store first. You would also not be able to export the certificate that you have loaded in CloudFront nor assign them to your EC2 or ELB instances as it would be tied to a single CloudFront distribution

### SWF

- You can use **markers** to record events in the workflow execution history for application specific purposes. Markers are useful when you want to record custom information to help implement decider logic. For example, you could use a marker to count the number of loops in a recursive workflow.

- Using **Signals** just enables you to inject information into a running workflow execution.

- Using **Timers** just enables you to notify your decider when a certain amount of time has elapsed.

- Likewise, using **Tags** just enables you to filter the listing of the executions when you use the visibility operations.

### Additional Point

- To configure an Application Load Balancer as a function trigger, grant Elastic Load Balancing permission to run the function, create a target group that routes requests to the function, and add a rule to the load balancer that sends requests to the target group.
