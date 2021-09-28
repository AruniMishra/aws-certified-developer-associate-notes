
-   DynamoDB:

    -   [https://aws.amazon.com/dynamodb/faqs/](https://aws.amazon.com/dynamodb/faqs/)

    -   DynamoDB TTL can delete items in a DDB table based on TTL values
        you specify.

    -   Amazon DynamoDB Accelerator (DAX):

        -   DAX (DynamoDB Accelerator) is a dedicated service designed
            to cache DynamoDB requests. It’ll automatically add the data
            in DAX if there’s a cache miss or update the data if it is
            updated in the table.

        -   fast in-memory performance, from single-digit milliseconds
            to microseconds

    -   For strongly consistent read requests from an application, DAX
        cluster passes all requests to DDB & does not cache for these
        requests.

    -   One read request unit represents one strongly consistent read
        request, or two * eventually consistent read requests, for an item
        up to 4KB in size.

    -   You want to read 540 items of 6KB each every minute using strong
        consistency mode, how many RCUs do you need?

        -   1 RCU provides 4KB read per second for strong consistency
            model

        -   so you’ll need 6KB/4KB = 1.50, round off to the nearest one,
            2 RCU

        -   540/60 = 9 items per second

        -   so total 9 \* 2 = 18 RCUs.

    -   Your app writes 150 items every second, each item is 4KB, how
        many WCU do you need?

        -   1 WCU provides 1KB write per second

        -   4KB/1KB = 4 WCU

        -   so, total 4 \* 150 = 600 WCUs

    -   Remember to round up to 1

        -   How many WCU is needed to support 100 writes per second of
            512 bytes?

        -   512/1024 = 0.5 -\> rounded up to 1, so we’ll need 1 \* 100 =
            100 WCU

    -   Make sure to be familiar with the simple math mentioned above,
        this is something you don’t want to miss in the real exam, it’s
        almost guaranteed at least one question will ask you to do this
        math. I got one, not two, not none, in mine. :)

    -   ***ProjectionExpression*** with Scan operation could fetch the
        entire table with only the columns mentioned in the
        ProjectionExpression attribute.

    -   Using the Global Secondary Index you can create a different pair
        of Partition and Sort key even after table creation for better
        search performance.

    -   GSI does not support Consistent Read, it only supports Eventual
        Read. For other tables, Query with Consistent Read will provide
        the latest results without scanning the whole table.

    -   BatchGetItem API allows you to pass multiple Partition Key
        values in a single request.

    -   S3 can be used to save items which are exceeding 400KB in size.
        Items saved in S3 while an object identifier is saved in DDB
        table which points to an item in S3.

    -   DynamoDB encryption is mandatory at the time of table creation
        and is of two types:

        -   DEFAULT method using “AWS owned CMK”

        -   KMS method using “AWS managed CMK”

    -   Parallel scans could be much faster than sequential scans. Also,
        its multiple worker threads in a background “sweeper” process
        scanning a table at a low priority without affecting production
        traffic.

\

-   RDS:

    -   You need to get the ***endpoint*** instead of IP to connect to
        the DB

    -   How to make sure the connection to the DB from the application
        is encrypted?

        -   Use SSL

    -   go through:
        [https://aws.amazon.com/rds/](https://aws.amazon.com/rds/) at
        the 1 min video on this page

\

-   AWS CLIs:

    -   [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/)

    -   Install it on your machine, play around with it, you’ll fall in
        love with it, and the knowledge and understanding you gain by
        hands-on experiences will be incredible and unforgettable.

    -   When using CLI, if the environment variables are set with access
        keys, they would take precedence over the IAM role.

    -   Chain:

        -   Environment variables - access key and secret key

        -   Java system properties

        -   default credential providers, located at \~/.aws/credentials

        -   custom created credentials file

        -   ECS container credentials

        -   check out this StackOverflow question and accepted answer
            that I asked years ago:
            [https://stackoverflow.com/questions/42798926/com-amazonaws-services-s3-model-amazons3exception-access-denied](https://stackoverflow.com/questions/42798926/com-amazonaws-services-s3-model-amazons3exception-access-denied)

\

-   AWS SDKs:

    -   [https://aws.amazon.com/tools/](https://aws.amazon.com/tools/)

    -   All ways of interactions boil down to an API call in the end.
        SDKs are the dominant/easiest/popular way to interact with AWS.

    -   How to use SDK to interact with the AWS services on the cloud?

        -   Create an IAM user, generate access keys, and then use them
            from within your program

        -   This is the best option since it is using IAM credentials to
            assume a user’s role when developing from the workstation.

    -   Access Keys consist of an access key ID and secret key which can
        be used to access AWS SDKs, REST or Query API operations.

\

-   CORS:

-   [https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html)

-   Along with API Gateway, how to configure CORS?

-   ACCESS-CONTROL-ALLOW-ORIGIN header with ‘\*’ or specific origins to
    fulfill pre-flight handshakes.

-   When using S3 to host a static website, a browser usually blocks
    Javascript from allowing the requests, but with CORS you can
    configure your bucket to explicitly enable cross-origin requests
    from your domain.

-   SQS:

-   THE very 1st message provided by AWS, yes, it’s not S3, it’s SQS. So
    no doubt about its usefulness and popularity, I’ve been using it
    since 2015 on a daily basis.

-   You’ll want to be really familiar with it, guaranteed 2+ questions
    (if not more) on SQS in real exams.

-   To delay seconds on individual messages, rather than on an entire
    queue, use ***message timers***.

-   [https://aws.amazon.com/sqs/faqs/](https://aws.amazon.com/sqs/faqs/)

\

-   CloudWatch:

-   In order to generate alarms on a ***10 second interval*** based on
    the published metrics, you’ll need to ***create high resolution
    metrics***.

-   Build in with most services that AWS offers, very useful for a lot
    of scenarios: troubleshooting, scheduling, etc.

-   [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)

-   [https://aws.amazon.com/cloudwatch/faqs/](https://aws.amazon.com/cloudwatch/faqs/)

\

-   SNS:

-   To ensure metadata can be sent along with messages, use SNS.

-   [https://aws.amazon.com/sns/faqs/](https://aws.amazon.com/sns/faqs/)

\

-   Step Functions:

-   Ensure to specify a ***timeout*** in state machine definitions.

-   If you are passing ***larger*** payloads between states, consider
    using ***S3***

-   [https://aws.amazon.com/step-functions/faqs/](https://aws.amazon.com/step-functions/faqs/)

\

-   AWS Secrets Manager:

-   makes it easier to manage secrets which can be DB creds, pw, API
    keys.

-   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)

\

-   EC2:

-   M2 General-Purpose instance, it provides network performance of
    10-25 Gbps based on instance type & enhanced Networking Support with
    ENA.

-   Which General-Purpose instance will support: in a ***Cluster
    Placement group*** to achieve a low latency network performance
    along with NVMe EBS:

    -   M5 and M5ad

-   [https://aws.amazon.com/ec2/faqs/](https://aws.amazon.com/ec2/faqs/)

\

-   S3:

-   [https://aws.amazon.com/s3/faqs/](https://aws.amazon.com/s3/faqs/)

-   S3 provides ***3.5k PUT*** requests per second ***per prefix*** in a
    bucket

-   S3 provides ***5.5k GET*** requests per second ***per prefix*** in a
    bucket

-   There’s no limit in the number of prefixes in a bucket.

-   Understand SSE with S3-Managed Keys (***SSE-S3***)

-   Cross-region replication can help which scenarios?

-   For S3 performance, you don’t need to randomize prefixes any more,
    and you can use sequential date-based naming for your prefixes. And
    use Cloudfront distribution in front of the S3 bucket.

-   If you see a significant increase in the number of HTTP-503
    responses from your S3 requests, you might have one or more objects
    in the bucket for which there are millions of versions.

    -   In this case, S3 automatically throttles your requests to the
        bucket to protect the customer from an excessive amount of
        request traffic.

    -   To determine which S3 objects have millions of versions, use the
        Amazon ***S3 Inventory tool*** which generates a report that
        provides a flat file list of the objects in a bucket.

\

-   AWS KMS:

-   [https://aws.amazon.com/kms/faqs/](https://aws.amazon.com/kms/faqs/)

-   Which approaches allow customers to manage the encryption keys?

    -   SSE with AWS KMS keys

    -   SSE with Customer-Provided Keys

    -   Client-Side Encryption

-   ***Asymmetric keys*** vs ***Symmetric keys*** in KMS.

-   How to provide temporary access to external auditors?

-   What is ***LocalCryptoMaterialsCache*** in KMS?

\

-   AWS DataPipeline:

-   ***Data Nodes***: define ***source*** and ***destination***
    locations

-   [https://aws.amazon.com/datapipeline/faqs/](https://aws.amazon.com/datapipeline/faqs/)

\

-   Amazon Redshift:

-   How to load data from S3 to Redshift?

    -   use ***COPY*** command.

-   [https://aws.amazon.com/redshift/faqs/](https://aws.amazon.com/redshift/faqs/)

\

-   AWS X-Ray:

-   [https://aws.amazon.com/xray/faqs/](https://aws.amazon.com/xray/faqs/)

-   Which environment variables are used by AWS Lambda to communicate
    with X-Ray?

    -   \_X\_AMZN\_TRACE\_ID

    -   AWS\_XRAY\_CONTEXT\_MISSING

        -   the AWS X-Ray SDK uses this variable to determine its
            behavior in the event that your function tries to record
            X-Ray data, but a tracing header is not available.

    -   AWS\_XRAY\_DAEMON\_ADDRESS

-   How to trace all incoming HTTP requests using X-Ray SDK?

    -   add ***Interceptors*** to your code to do so.

-   How to use X-ray to debug Lambda:

    -   make sure the IAM role assigned to the Lambda function has
        access to the ***X-ray*** service

    -   to enable tracing on your Lambda function using the Lambda CLI:

        -   find the execution role of your Lambda function

        -   attach the following managed policy: AWSXrayWriteOnlyAccess

-   for default Sampling using X-Ray console:

    -   ***one request per second*** & **five percent of any additional
        request per host.**

-   Understand sampling rules:
    [https://docs.aws.amazon.com/xray/latest/devguide/xray-api-sampling.html](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-sampling.html)

\

-   AWS CloudFormation:

-   [https://aws.amazon.com/cloudformation/faqs/](https://aws.amazon.com/cloudformation/faqs/)

-   Supported data type for a Parameters are as follows:

    -   String

    -   Number

    -   List

    -   CommaDelimitedList

    -   AWS-Specific Parameter Types

    -   SSM Parameter Types

-   How to test a new function with migrating 5% of traffic to a new
    version?

    -   aws lambda create-alias --name alias name --function-name
        function-name --routing-config AdditionalVersionWeights={“2” =
        0.05}

-   How to make templates that can be used across multiple accounts and
    regions with the least amount of effort?

    -   Use CloudFormation ***StackSets***.

-   CF can be used to automate the deployment of the Code Deployment
    environment itself (on AWS CodeDeploy).

-   How to have instances preconfigured with NGINX web server?

    -   use ***cfn-init*** helper script in CloudFormation

\

-   AWS Route53:

-   Route 53 Weighted Routing policy could be used to manage Blue Green
    deployment.

-   [https://aws.amazon.com/route53/faqs/](https://aws.amazon.com/route53/faqs/)

\

-   AWS CodeDeploy:

-   [https://aws.amazon.com/codedeploy/faqs/](https://aws.amazon.com/codedeploy/faqs/)

-   use the aws ssm get-parameters with the --with-description option

-   give permission to the AWS CodeDeploy service via an IAM role

-   While creating event hooks for Blue/Green deployment, following
    events can have scripted files:

    -   ApplicationStop

    -   BeforeInstall

    -   AfterInstall

    -   ApplicationStart

    -   ValidationService

    -   BeforeAllowTraffic

    -   AfterAllowTraffic

    -   BeforeBlockTraffic

    -   AfterBlockTraffic

-   Right sequence of hooks gets called:

    -   ApplicationStop -\> BeforeInstall -\> AfterInstall -\>
        ApplicationStart

-   Immutable updates:

    -   are an alternative to rolling updates where a temporary ASG is
        launched outside of your environment with a separate set of
        instances running on the new configuration, which are placed
        behind your environment’s load balancer. Old and new instances
        both serve traffic until the new instance passes health checks,
        at which time the new instances are moved into your
        environment’s ASG and the temporary group and old instances are
        terminated.

-   Rolling updates:

    -   has down time. However, it’s not for the entire fleet, it’ll be
        divided into batches.

    -   when a configuration change requires instances to be replaced.
        Elastic Beanstalk can perform the update in batches to avoid
        downtime while the change is propagated. During a rolling
        update, capacity is only reduced by the size of a single batch,
        which you can configure. Elastic Beanstalk takes one batch out
        of service, terminates them, and then launches a batch with the
        new configuration. After the new batch starts serving requests.
        Elastic Beanstalk moves on to the next batch.

-   Which deployment method could let the fleet maintain its full
    capacity?

    -   Immutable

    -   Rolling with additional batch

-   Canary release deployment:

    -   total traffic is separated at random into a production release
        and a canary release with a pre-configured ratio.

-   Blue Green Deployment:

    -   either use Route 53 Weighted Routing policy

    -   or use *Elastic Beanstalk* with the ***swap URL*** feature

    -   You can avoid downtime by using blue/green deployment, where you
        deploy a new version to a separate environment, and then swap
        CNAMEs of the two environments to redirect traffic to the new
        version instantly.

-   Which file is used to specify how your application will be deployed
    to the underlying instances?

    -   AppSpec.YAML

-   Where to specify the right version of your application to be
    deployed?

    -   AppSpec file in either JSON or YAML format

\

-   API Gateway:

    -   [https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)

    -   When your API’s resources receive requests from a domain other
        than the API’s own domain, you must enable cross-origin resource
        sharing (CORS) for selected methods on the resource. This
        amounts to having your API respond to the OPTIONS preflight
        request with at least the following CORS-required response
        headers.

        -   ACCESS-CONTROL-ALLOW-METHODS

        -   ACCESS-CONTROL-ALLOW-HEADERS

        -   ACCESS-CONTROL-ALLOW-ORIGIN

\

-   CloudFront:

    -   [https://aws.amazon.com/cloudfront/faqs/](https://aws.amazon.com/cloudfront/faqs/)

    -   when to configure your origin to add a ***Cache-Control or an
        Expires header*** field to each object

    -   when to specify a value for ***Minimum TTL in CloudFront
        cache*** behaviors

    -   To configure CloudFront to require HTTPS between viewers and
        CloudFront

        -   redirect HTTP to HTTPS

        -   HTTPS only

    -   To configure CloudFront to require HTTPS between CloudFront and
        your custom origin:

        -   change the Origin Protocol Policy to HTTPS only.

\

-   AWS Cognito:

    -   [https://aws.amazon.com/cognito/faqs/](https://aws.amazon.com/cognito/faqs/)

    -   Blocking compromised users is Advanced Security setting

    -   Understand Cognito User Pool vs Cognito Identity Pool

\

-   IAM:

    -   [https://aws.amazon.com/iam/faqs/](https://aws.amazon.com/iam/faqs/)

    -   At least 4+ questions around IAM in real exams. Since every
        application needs authentication and authorization. You cannot
        go around it. :)

\

-   OpsWork:

    -   [https://aws.amazon.com/opsworks/stacks/faqs/](https://aws.amazon.com/opsworks/stacks/faqs/)

    -   is best suited when you have ***multiple stacks*** and want to
        use configuration tools for the environment

\

-   Elastic Cache:

    -   [https://aws.amazon.com/elasticache/faqs/](https://aws.amazon.com/elasticache/faqs/)

    -   Memcached does not offer high availability (replication) while
        Redis does on AWS ElastiCache

\

-   Elastic Beanstalk:

    -   [https://aws.amazon.com/elasticbeanstalk/faqs/](https://aws.amazon.com/elasticbeanstalk/faqs/)

    -   When to use Packer?

    -   supports two options of saving configuration options: YAML or
        JSON, both can be included in your application’s source file in
        a directory named ***.ebextensions*** and deployed as part of
        your application code

    -   How to change instance types in a prod environment with AWS
        Elastic Beanstalk?

    -   How to move an existing dev env which consists mainly of Docker
        based containers to AWS cloud?

    -   When you use the Elastic Beanstalk console to deploy a new
        application or an application version, you’ll need to upload a
        source bundle. Your source must meet the following requirements:

        -   Consist of a single ZIP file or WAR file

        -   Not exceed 512 MB

        -   Not include a parent folder

    -   If you want to deploy a worker application that processes
        ***periodic*** background tasks, your application source bundle
        must also include a ***cron.yaml*** file.

    -   The least time needed way to provision an application using
        Elastic Beanstalk with unique configuration files and software
        to include:

        -   use a custom ***AMI*** for the underlying instances

\

-   Lambda and Serverless:

    -   [https://aws.amazon.com/lambda/faqs/](https://aws.amazon.com/lambda/faqs/)

    -   How to make a function run on a scheduled basis?

        -   Use CloudWatch events

    -   DynamoDBCrudPolicy will give CRUD permissions to a DynamoDB
        table which is tighter and inline with best security practice
        with the least privilege.

    -   Avoid using recursive code in your Lambda function, this is
        clearly documented in AWS docs. Since it could lead to
        unintended volume of function invocations and escalated costs.

\

-   AWS CodePipeline:

    -   [https://aws.amazon.com/codepipeline/faqs/](https://aws.amazon.com/codepipeline/faqs/)

\

-   AWS CodeBuild:

    -   [https://aws.amazon.com/codebuild/faqs/](https://aws.amazon.com/codebuild/faqs/)

    -   How to allow an application that needs to access an RDS instance
        in a private subnet when using CodeBuild to build?

        -   provide ***additional VPC-specific configuration
            information*** as part of your AWS CodeBuild project, e.g.
            VPC ID, subnet IDs, SG IDs

    -   supports building at the following locations:

        -   AWS CodeCommit

        -   S3

        -   Github

        -   BitBucket

\

-   AWS CodeCommit:

    -   [https://aws.amazon.com/codecommit/faqs/](https://aws.amazon.com/codecommit/faqs/)

    -   You can migrate to CodeCommit from other version control
        systems, but you’ll have to migrate to Git first.

    -   You can migrate to CodeCommit in a number of ways: by cloning
        it, mirroring it, migrating all or just some of the branches and
        so on.

    -   How to share repo easily and securely?

\

-   Kinesis:

    -   [https://aws.amazon.com/kinesis/](https://aws.amazon.com/kinesis/)

    -   You cannot guarantee the order of data across multiple shards.
        It’s possible only within a shard.

    -   Transferring data via SSL is for data in transit.

    -   How to stream large sets of data directly into S3?

        -   Use Kinesis Data Firehose.

    -   How to analyze data using standard SQL?

        -   Amazon ***Kinesis Data Analytics***

\

-   Amazon ECS:

    -   [https://aws.amazon.com/ecs/faqs/](https://aws.amazon.com/ecs/faqs/)

    -   Where to store Docker images?

        -   ECR

        -   and

        -   Docker Hub

        -   but not store them as AMI

    -   ECS has orchestration built-in.

\

-   Jenkins/CI/CD:

    -   [https://aws.amazon.com/getting-started/projects/set-up-ci-cd-pipeline/](https://aws.amazon.com/getting-started/projects/set-up-ci-cd-pipeline/)

