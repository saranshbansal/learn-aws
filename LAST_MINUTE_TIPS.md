Common HTTP response codes:

- 501 - Not implemented (elb transfer enconding not supported)
- 502 - Bad gateway (response from integration was in bad format)
- 503 - Service unavailable (no registered targets in auto scaling group)
- 504 - Gateway timeout (Lambda function was running for more than 29s)
- 429 - Throttling / Too many requests
- 401 - Unauthorized
- 403 - Access denied (Invalid authorization token)

## IAM / STS

- Trust policy is a resource based policy for an IAM role
- STS can be enabled/disabled per region

## CLI

- Credentials stored manually in .aws/credentials file or Enviroment variables take precedence over the IAM role assigned to an EC2 instance

## EC2

- Zonal reserved instances provide capacity reservation in the specified availability zone. Regional reserved instances **DO NOT**.
- Here is the correct way of reusing SSH keys in your AWS Regions:
    - Generate a public SSH key (.pub) file from the private SSH key (.pem) file.
    - Set the AWS Region you wish to import to.
    - Import the public SSH key into the new Region.
- A Reserved Instance billing benefit can apply to a maximum of 3600 seconds (one hour) of instance usage per clock-hour. You can run multiple instances concurrently, but can only receive the benefit of the Reserved Instance discount for a total of 3600 seconds per clock-hour; instance usage that exceeds 3600 seconds in a clock-hour is billed at the On-Demand rate.

## EBS

- If an EBS volume is the root device of an instance, you must **stop the instance before you can detach the volume.**
- Support both in-flght (from instance to volume) and at rest encryption
- When an instance is running, you cannot change the “Delete on termination” attribute on the root volume. You must use cli to change `DeleteOnTermination` attribute to false
- The maximum ratio of provisioned IOPS to requested volume size (in GiB) is 50:1
- Encryption by default is a Region-specific setting. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region.
- AWS CloudTrail event logs for '**CreateVolume**' aren't available for EBS volumes created during an Amazon EC2 launch.

## Load Balancing

- Cross-zone load balancing is enabled by default
- ALB access logs provide info about requests sent to the load balancer
- Target types
    - Instance
    - ip
    - Lambda
- When the target type is IP, you can specify IP addresses from specific CIDR blocks only. You can't specify publicly routable IP addresses.

## S3

- Transfer acceleration requires DNS compliant name, it must not contain periods
- Use multipart upload API when object size exceeds 100MB
- When using SSE-C, S3 stores a randomly salted HMAC value of the encryption key you provide.
- If you lose the encryption key, you lose the object.
- When trying to upload a large file to your S3 bucket with an upload request that includes an AWS KMS key, then you have to confirm that you have permission to perform `kms:Decrypt` actions on the AWS KMS key that you’re using to encrypt the object.

- To enable the cross-region replication feature in S3, the following items should be met:
    - The source and destination buckets must have **versioning** enabled.
    - The source and destination buckets must be in different AWS Regions.
    - Amazon S3 must have permissions to replicate objects from that source bucket to the destination bucket on your behalf
- Replication
    - Lifecycle actions are not replicated, and if you want the same lifecycle configuration applied to both source and destination buckets, enable the same lifecycle configuration on both.
    - Same-Region Replication (SRR) and Cross-Region Replication (CRR) can be configured at the S3 bucket level, a shared prefix level, or an object level using S3 object tags
- Headers for SSE-S3
    - x-amz-server-side-encryption: AES256
- Headers for SSE-KMS
    - x-amz-server-side-encryption: aws:kms
- Headers for SSE-C
    - x-amz-server-side-encryption-customer-algorithm: AES256
    - x-amz-server-side-encryption-customer-key
    - x-amz-server-side-encryption-customer-key-MD5
- ACLs are service policies that allow you to control which principals in another account can access a resource
- By default, when other AWS accounts upload objects to your bucket, the objects remain owned by the uploading account. With S3 Object Ownership, any new objects that are written by other accounts with the bucket-owner-full-control canned access control list (ACL) automatically become owned by the bucket owner, who then has full control of the objects.
    
    S3 Object Ownership has two settings: 1. Object writer – The uploading account will own the object. 2. Bucket owner preferred – The bucket owner will own the object if the object is uploaded with the `bucket-owner-full-control` canned ACL. Without this setting and canned ACL, the object is uploaded and remains owned by the uploading account.
    
- If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent. If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket. With versioning, every successful write will create a new version of your object and will also send event notification.
- **S3 Consistency Model**
    - Bucket configurations have an **eventual** consistency model. If you delete a bucket and immediately list all buckets, the deleted bucket might still appear in the list.
    - Amazon S3 provides **strong** read-after-write consistency for PUTs and DELETEs of objects in your Amazon S3 bucket in all AWS Regions. This applies to both writes to new objects as well as PUTs that overwrite existing objects and DELETEs.
    - If you enable versioning on a bucket for the first time, it might take a short amount of time for the change to be fully propagated. We recommend that you wait for 15 minutes after enabling versioning before issuing write operations (PUT or DELETE requests) on objects in the bucket.

## CloudFront

- CloudFront has a default SSL certificate when using CloudFront provided domain (for HTTPS between the viewer and CloudFront)
- Cloudfront functions support JavaScript, can be invoked at viewer request / response
- Lambda@Edge functions support Python and JavaScript, can be invoked at both origin and viewer request / response
- Use versioned file names in S3 to update the files without cache invalidation
- Signed URL / Cookie
    - CloudFront key pairs can be created by root account only. Max 2 key-pairs. Can only be created from the console
    - Signed URLs take precedence over signed cookies
- CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin. CloudFront only sends requests to the secondary origin after a request to the primary origin fails.
- CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD, or OPTIONS. CloudFront does not failover when the viewer sends a different HTTP method (for example POST, PUT, and so on).

## Elastic Beanstalk

- Supported platforms - Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker web
- If your environment connects to a decoupled RDS database, remove the database’s security group rule that allows ingress from ASG’s security group.
- Include `env.yaml` in the root of your application source bundle to configure the environment name, solution stack and environment links to use when creating your environment.
- `Dockerrun.aws.json` - for creating multicontainer docker environment.
- `Immutable` deployment creates a new set of instances, while Blue/Green deployment creates a new environment. Use swap environment URL option in console
- If you terminate instances from the failed deployment, Elastic Beanstalk replaces them with instances running the application version from the most recent successful deployment.
- Application revision / version quota (`1000`) is applied to all applications within a region
- `Immutable` is the longest deployment, `AllAtOnce` is the fastest.
- Only supports `zip` or `war` files for deployment. NOT `tar`.

## ECS

- Create task using
    - `RunTask` - placed using **random**
    - `CreateService` - placed using **spread** with `ecs.availability-zone` attribute
- `ECS_CLUSTER` env variable determines which cluster the ECS agent should check into. This can be set via user data script when using EC2 launch type
- `ECS_ENABLE_TASK_IAM_ROLE` ****- This configuration item is used to enable IAM roles for tasks for containers with the bridge and default network modes.
- Amazon ECS metric data is automatically sent to CloudWatch in 1-minute periods. Also, any Amazon ECS service using the Fargate launch type has CloudWatch CPU and memory utilization metrics automatically, so you don't need to take any manual steps.
- Amazon ECR users require permission to call `ecr:GetAuthorizationToken` before they can authenticate to a registry and push or pull any images from any Amazon ECR repository.

## SQS

- Default visibility timeout is 30s. Max 12h.
- The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.
- Default message retention period s 4d, max is 14d
- Max message size 256KB
- Consumer can receive max 10 messages at a time
- `ReceiveMessageWaitTimeSeconds` > 0 for long polling. Min 0s, max 20s.
- SQS FIFO queues don't accept messages with same `MessageDeduplicationID` during a 5 minute deduplication interval.
- Default FIFO deduplication interval is 5 mins. Duplicate messages are accepted during this but not delivered to consumers
- DLQ of FIFO has to be FIFO, and DLQ of standard has to be standard
- maximum of approximately 120,000 inflight messages (received from a queue by a consumer, but not yet deleted from the queue)
- When you request messages from a queue, you can't specify which messages to retrieve. Instead, you specify the maximum number of messages (up to 10) that you want to retrieve.

## Kinesis

- `PutRecords` API doesn’t guarantee ordering, because a single record processing failure doesn’t affect the batch
- For ordering use `PutRecord` to write a single record along with the `SequenceNumberForOrdering` parameter
- Include a unique ID in records to handle duplicates
- Data records are actually re-routed to flow to the child shards based on the hash key values that the data-record partition keys map to. Hence, records are not lost.
- When using Lambda to consume streams, the maximum concurrent executions is equal to the number of shards in the stream.
- Records are stored for 24h
    - extended data retention - 7d
    - long-term data retention - upto 365d
- **Kinesis agent** continuously monitors a set of files and sends new data to your stream. It also emits Amazon CloudWatch metrics to help you better monitor and troubleshoot the streaming process.

## VPC

- The default NACL **allows** all inbound and outbound traffic by default
- Security groups allow all **outbound** traffic by default
- Linux kernels (including the Amazon Linux kernel) use ports 1025-5000. ELB uses the port range 1024-65535.

## RDS

- RDS supports using Transparent Data Encryption (TDE) to encrypt stored data on your DB instances running Microsoft SQL Server. TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage.
- Enhanced Monitoring - Provides metrics (CPU bandwidth, memory consumed etc) for different processes running on RDS DB instance
- Read Replica data replication is asynchronous
- PostgreSQL, MySQL, Aurora, MariaDB support IAM auth
- RDS applies OS updates by performing maintenance on the standby, then promoting the standby to primary and finally performing maintenance on the old primary, which becomes the new standby
- Automated backups are limited to a single AWS Region while manual snapshots and Read Replicas are supported across multiple Regions.
    - RDS automatic backup retention period is max 35 days
- In addition to strings, Redis supports lists, sets, sorted sets, hashes, bit arrays, and hyperlog logs. Applications can use these more advanced data structures to support a variety of use cases. For example, you can use **Redis Sorted Sets** to easily implement a game leaderboard that keeps a list of players sorted by their rank. Memacached does not support advanced data structures.

## ElastiCache

- All the nodes in a Redis cluster (cluster mode enabled or cluster mode disabled) must reside in the same region.
- While using Redis with cluster mode enabled, there are some limitations:
    - You cannot manually promote any of the replica nodes to primary.
    - Multi-AZ is required.
    - You can only change the structure of a cluster, the node type, and the number of nodes by restoring from a backup.
- When you add a read replica to a cluster, all of the data from the primary is copied to the new node. From that point on, whenever data is written to the primary, the changes are asynchronously propagated to all the read replicas, for both the Redis offerings (cluster mode enabled or cluster mode disabled).
- If you have no replicas and a node fails, you experience loss of all data in that node's shard, when using Redis with cluster mode enabled. If you have no replicas and the node fails, you experience total data loss in Redis with cluster mode disabled.

## X-RAY

- X-RAY daemon uses `AWSXRayDaemonWriteAccess` policy to upload traces
- `AWSXRayReadOnlyAccess` for viewing traces in the console

## CloudWatch

- High resolution metrics have 1 second granularity
- Standard resolution metrics have 1 minute granularity
- You can alert with High-Resolution Alarms, as frequently as 10-second periods.
- To associate the CMK with an existing log group, you can use the `associate-kms-key` command.

## CloudTrail

- CloudTrail always delivers object-level API access logs to the requester. In addition, CloudTrail also delivers the same logs to the bucket owner only if the bucket owner has permissions for the same API actions on that object.

## API Gateway

- To invalidate API cache, the client must have **`execute-api:InvalidateCache`** permission
- Integration max timeout is 29s. After that APIGW returns `504 gateway timeout` error
- CloudWatch metrics -
    - IntegrationLatency
    - Latency
    - Count
    - CacheHitCount (when API cache is enabled)
    - CacheMissCount (when API cache is enabled)
- Your account is charged for accessing method-level CloudWatch metrics, but not the API-level or stage-level metrics.
- In access logging, only $context variables are supported

## Lambda

- Supported languages - Java, Go, PowerShell, Node.js, C#, Python, and Ruby
- Lambda function needs `AWSLambdaDynamoDBExecutionRole` to poll records from a DynamoDB stream
- Lambda functions that use multi-architecture container images are **NOT** supported. They must target a single architecture
- Provisioned concurrency can be auto-scaled on a schedule
- Lambda Environment variables must not exceed 4 KB
- Max memory (RAM) is 10240MB (10GB)
- Provisioned Concurrency cannot be used with the `$LATEST` version.
- Reserving concurrency is free, but Provisioned concurrency is charged for
- Lambda@Edge functions can only be created in `us-east-1` region. Lambda will replicate your function in locations all around the world.
    - If you try to create a Lambda@Edge function via CloudFormation in a region other than `us-east-1`, the stack creation will **FAIL**
- **Lambda Throttling**: The following are the recommended solutions to handle throttling issues:
  - **Configure reserved concurrency** – by default, there are `900` unreserved concurrencies shared across all functions in a region. To prevent other functions from consuming the available concurrent executions, reserve a portion of it to your Lambda function based on the demand of your current workload.
  - **Use exponential backoff in your app** – a technique that uses progressively longer waits between retries for consecutive error responses. This can be used to handle throttling issues by preventing collision between simultaneous requests.
  - **Use a dead-letter queue** – If you’re using Amazon S3 and Amazon EventBridge (Amazon CloudWatch Events), configure your function with a dead letter queue to catch any events that are discarded due to constant throttles. This can protect your data if you’re seeing significant throttling.
  - **Request a service quota increase** – you can reach AWS support to request for a higher service quota for concurrent executions.
- **Step Functions** - If you want to manage multiple Lambda functions that invoke one another, use `Step Functions` which use `State Machine`, a technique in modeling systems whose output depends on the entire history of their inputs, not just on the most recent input. In this case, the Lambda functions invoke one another, creating a large state machine.

## DynamoDB

- Streams records are stored for 24h
- Max 5 LSI and 20 GSI per table
- GSI does not support strongly consistent reads
- Max data of 10GB per partition key value for LSI and table
- `BatchWriteItem` cannot update items, i.e. doesn't support `UpdateItem` operation
- `BatchGetItem` returns failed reads as `UnprocessedKeys`
- DynamoDB has two built-in backup methods (On-demand, Point-in-time recovery) that write to Amazon S3, but you will **NOT** have access to the S3 buckets that are used for these backups.

## Cognito

- User pool integrates with API Gateway and Application Load Balancer

## Step Functions

- Max execution duration
    - Standard - 365d
    - Express - 5min
- Express Workflows do not support activities, job-run (.sync), and Callback patterns.

## CodeCommit

- `codecommit:GitPull` and `codecommit:GitPush` IAM permissions are for git client and not for API actions. They are used for git clone, git fetch, git pull and git push.

## CodeBuild

- You can use the AWS CodeBuild agent to test and debug builds on a local machine.

## CodeDeploy

- Deployment types
    - EC2 - In-Place, Blue/Green
    - On Premises - In-place
    - Lambda - Blue/Green
    - ECS - Blue/Green
- Lifecycle hooks for Lambda deployment
    - BeforeAllowTraffic
    - AfterAllowTraffic
- CodeDeploy only uses blue/green for Lambda deployments
- CodeDeploy agent is only needed for EC2/On-Premises platform
- CodeDeploy agent communicates outbound on HTTPS port 443
- Required properties in `resources` section of `appspec.yaml` file for ECS deployment
    - TaskDefinition
    - ContainerName
    - ContainerPort
- You can use the **:max_revisions:** option in the agent configuration file to specify the number of application revisions to the archive by entering any positive integer. CodeDeploy also archives the log files for those revisions. All others are deleted, except for the log file of the last successful deployment.
- You can configure CodeDeploy to auto-update on boot by setting the `AUTOUPDATE` variable to true.

## CodePipeline

- Approval actions can't be added to Source stages. Source stages can contain only source actions.

## KMS / Security

- AWS Encryption SDK encrypts your data using a **symmetric** key algorithm
- SSM Parameters do not support resource based policies. Advanced tier parameters support parameter policies
- Symmetric keys with imported key material cannot be automatically rotated. Keys with AWS generated key material can be.

## Route 53

- An Alias record can only point to AWS services

## CloudFormation

- Conditions cannot be used in Parameters section
- Exports must have unique names within a single Region
- StackSets are regional resources
- To prevent deletion of successfully provisioned resources on stack failure, use `--disable-rollback` option while creating or updating a stack from CLI

## CDK

- Supported languages - JavaScript/TypeScript, Python, Java, and .NET

## SAM

- To deploy an application that contains one or more nested applications, you must include the `CAPABILITY_AUTO_EXPAND` capability in the sam deploy command.