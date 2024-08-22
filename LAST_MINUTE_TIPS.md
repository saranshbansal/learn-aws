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
- A `launch template` is an instance configuration template that an Auto Scaling group uses to launch EC2 instances. Modifying an existing launch template is not possible.
- Run custom shell scripts while launching EC2 instance: use `user data` while launching EC2

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

- Transfer acceleration requires DNS compliant name, **it must not contain periods**
- Use **multipart upload** API when object size exceeds `100MB`
- Use `Amazon S3 Object Lambda` to redact PII before it is returned to the application.
- When using `SSE-C`, S3 stores a randomly salted `HMAC` value of the encryption key you provide.
- If you lose the encryption key, you lose the object.
- When trying to upload a large file to your S3 bucket with an upload request that includes an AWS KMS key, then you have to confirm that you have permission to perform `kms:Decrypt` actions on the AWS KMS key that you’re using to encrypt the object.

- To enable the cross-region replication feature in S3, the following items should be met:
    - The source and destination buckets must have **versioning** enabled.
    - The source and destination buckets must be in different AWS Regions.
    - Amazon S3 must have permissions to replicate objects from that source bucket to the destination bucket on your behalf
- Replication
    - Lifecycle actions are not replicated, and if you want the same lifecycle configuration applied to both source and destination buckets, enable the same lifecycle configuration on both.
    - Same-Region Replication (SRR) and Cross-Region Replication (CRR) can be configured at the S3 bucket level, a shared prefix level, or an object level using S3 object tags
- Headers for `SSE-S3`
    - `x-amz-server-side-encryption: AES256`
- Headers for `SSE-KMS`
    - `x-amz-server-side-encryption: aws:kms`
- Headers for `SSE-C`
    - `x-amz-server-side-encryption-customer-algorithm: AES256`
    - `x-amz-server-side-encryption-customer-key`
    - `x-amz-server-side-encryption-customer-key-MD5`

- `ACLs` are service policies that allow you to control which principals in another account can access a resource
- By default, when other AWS accounts upload objects to your bucket, the objects remain owned by the uploading account. 
- With S3 Object Ownership, any new objects that are written by other accounts with the bucket-owner-full-control canned access control list (ACL) automatically become owned by the bucket owner, who then has full control of the objects.
- S3 Object Ownership has two settings: 
  - **Object writer** – The uploading account will own the object. 
  - **Bucket owner preferred** – The bucket owner will own the object if the object is uploaded with the `bucket-owner-full-control` canned ACL. Without this setting and canned ACL, the object is uploaded and remains owned by the uploading account.
- If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent. If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket. With versioning, every successful write will create a new version of your object and will also send event notification.
- `Retention` option can be set while defining S3 lifecycle options for limiting old artifacts versions by number or age. This can prevent deleting source bundle.

- **S3 Consistency Model**
    - Bucket configurations have an **eventual** consistency model. If you delete a bucket and immediately list all buckets, the deleted bucket might still appear in the list.
    - Amazon S3 provides **strong** `read-after-write` consistency for `PUTs` and `DELETEs` of objects in your Amazon S3 bucket in all AWS Regions. This applies to both writes to new objects as well as `PUTs` that overwrite existing objects and `DELETEs`.
    - If you enable versioning on a bucket for the first time, it might take a short amount of time for the change to be fully propagated. We recommend that you wait for 15 minutes after enabling versioning before issuing write operations (PUT or DELETE requests) on objects in the bucket.

## CloudFront

- CloudFront has a default SSL certificate when using CloudFront provided domain (for HTTPS between the viewer and CloudFront)
- Cloudfront functions support `JavaScript`, can be invoked at viewer request / response
- `Lambda@Edge` functions support `Python` and `JavaScript`, can be invoked at both origin and viewer request / response
- Use versioned file names in S3 to update the files without cache invalidation
- **Signed URL / Cookie**
    - CloudFront key pairs can be created by root account only. **Max 2 key-pairs**. Can only be created from the console
    - Signed URLs take precedence over signed cookies
- CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin. CloudFront only sends requests to the secondary origin after a request to the primary origin fails.
- CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is **GET, HEAD, or OPTIONS**. CloudFront does not failover when the viewer sends a different HTTP method (for example POST, PUT, and so on).
- **Update images/objects in CloudFront - General -** Update the images by invalidating them from the edge caches.
- **Update images/objects in CloudFront - Cost-effective -** With **versioning**, you don’t have to wait for an object in cache to expire before CloudFront begins to serve a new version of it, and you don’t have to pay for object invalidation.

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
- If you terminate a container instance in the `RUNNING` state, that container instance is automatically removed or deregistered from the cluster. However, if you terminate a container instance in the `STOPPED` state, that container instance isn’t automatically removed from the cluster.

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

- All the nodes in a Redis cluster (cluster mode enabled or cluster mode disabled) must reside in the **same region**.
- While using Redis with cluster mode enabled, there are some limitations:
    - You cannot manually promote any of the replica nodes to primary.
    - Multi-AZ is required.
    - You can only change the structure of a cluster, the node type, and the number of nodes by restoring from a backup.
- When you add a read replica to a cluster, all of the data from the primary is copied to the new node. From that point on, whenever data is written to the primary, the changes are asynchronously propagated to all the read replicas, for both the Redis offerings (cluster mode enabled or cluster mode disabled).
- If you have no replicas and a node fails, you experience loss of all data in that node's shard, when using Redis with cluster mode enabled. If you have no replicas and the node fails, you experience total data loss in Redis with cluster mode disabled.
- You can choose `Memcached` over `Redis` if you have the following requirements:
  - You need the simplest model possible.
  - You need to run large nodes with **multiple cores or threads**.
  - You need the ability to scale out and in, adding and removing nodes as demand on your system increases and decreases.
  - You need to cache objects, such as a database.

## X-RAY

- X-RAY daemon uses `AWSXRayDaemonWriteAccess` policy to upload traces
- `AWSXRayReadOnlyAccess` for viewing traces in the console
- Enabling tracing on various computes:
    | Type              | Method                                                                                                                                |
    | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
    | ECS Cluster       | Create a **Docker image** that runs the X-Ray daemon                                                                                  |
    | Elastic BeanStalk | Enable the X-Ray daemon by including the `xray-daemon.config` configuration file in the `.ebextensions` directory of your source code |
    | EC2 instances     | Using a **user data script** to run the daemon automatically                                                                          |
    | Lambda            | update the lambda function and add `--tracing-config Mode=Active`                                                                     |

- AWS Lambda uses environment variables to facilitate communication with the X-Ray daemon and configure the X-Ray SDK.
  - **_X_AMZN_TRACE_ID**: Contains the tracing header, which includes the `sampling decision`, `trace ID`, and `parent segment ID`. If Lambda receives a tracing header when your function is invoked, that header will be used to populate the _X_AMZN_TRACE_ID environment variable. If a tracing header was not received, Lambda will generate one for you.

  - **AWS_XRAY_CONTEXT_MISSING**: The X-Ray SDK uses this variable to determine its behavior in the event that your function tries to record X-Ray data, but a tracing header is not available. Lambda sets this value to `LOG_ERROR` by default.

  - **AWS_XRAY_DAEMON_ADDRESS**: This environment variable exposes the X-Ray daemon’s address in the following format: IP_ADDRESS:PORT. You can use the X-Ray daemon’s address to send trace data to the X-Ray daemon directly without using the X-Ray SDK.
- Use **annotations** to record data that you want to use to group traces. Also, use it for filtering traces as it is indexed.

## CloudWatch

- High resolution metrics have 1 second granularity
- Standard resolution metrics have 1 minute granularity
- You can alert with High-Resolution Alarms, as frequently as 10-second periods.
- To associate the CMK with an existing log group, you can use the `associate-kms-key` command.
- By default doesn't track memory utilization metric of instances.
- When you create an alarm, you specify three settings to enable CloudWatch to evaluate when to change the alarm state:
  - **Period** is the length of time to evaluate the metric or expression to create each individual data point for an alarm. It is expressed in seconds. If you choose one minute as the period, there is one datapoint every minute.
  - **Evaluation Period** is the number of the most recent periods, or data points, to evaluate when determining alarm state.
  - **Datapoints to Alarm** is the number of data points within the evaluation period that must be breaching to cause the alarm to go to the ALARM state. The breaching data points do not have to be consecutive, they just must all be within the last number of data points equal to Evaluation Period.
  - CloudWatch **doesn't cover memory** usage by default. It only captures CPU usage for EC2 instances. To enable memory usage metric, install CW agent in EC2 instances.

## CloudTrail

- CloudTrail always delivers object-level API access logs to the requester. In addition, CloudTrail also delivers the same logs to the bucket owner only if the bucket owner has permissions for the same API actions on that object.

## API Gateway

- To invalidate API cache, the client must have **`execute-api:InvalidateCache`** permission
- The client must send a request that contains the `Cache-Control: max-age=0` header to invalidate the cache.
- Ticking the `Require authorization` checkbox ensures that not every client can invalidate the API cache.
- Integration max timeout is `29s`. After that APIGW returns `504 gateway timeout` error.
- CloudWatch metrics -
    - IntegrationLatency
    - Latency
    - Count
    - CacheHitCount (when API cache is enabled)
    - CacheMissCount (when API cache is enabled)
- Your account is charged for accessing method-level CloudWatch metrics, but not the API-level or stage-level metrics.
- In access logging, only `$context` variables are supported.
- The following are the Gateway response types which are associated with the HTTP 504 error in API Gateway:
  - **INTEGRATION_FAILURE** – The gateway response for an integration failed error. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type.
  - **INTEGRATION_TIMEOUT** – The gateway response for an integration timed-out error. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type.
- **Lambda Authorisers**: A Lambda authorizer is an API Gateway feature that uses a Lambda function to control access to your API. When a client makes a request to one of your API’s methods, API Gateway calls your Lambda authorizer, which takes the caller’s identity as input and returns an IAM policy as output. There are two types of Lambda authorizers:
  - A **token-based** Lambda authorizer (also called a **TOKEN authorizer**) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
  - A **request parameter-based** Lambda authorizer (also called a **REQUEST authorizer**) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.

## Lambda

- Supported languages - Java, Go, PowerShell, Node.js, C#, Python, and Ruby
- Lambda function needs `AWSLambdaDynamoDBExecutionRole` to poll records from a DynamoDB stream
- Lambda functions that use multi-architecture container images are **NOT** supported. They must target a single architecture
- Provisioned concurrency can be auto-scaled on a schedule
- Lambda Environment variables must not exceed `4 KB`
- Max memory (RAM) is `10240MB (10GB)`
- Provisioned Concurrency cannot be used with the `$LATEST` version.
- Reserving concurrency is free, but Provisioned concurrency is charged for
- `Lambda@Edge` functions can only be created in `us-east-1` region. Lambda will replicate your function in locations all around the world.
    - If you try to create a Lambda@Edge function via CloudFormation in a region other than `us-east-1`, the stack creation will **FAIL**
- **Lambda Throttling**: The following are the recommended solutions to handle throttling issues:
  - **Configure reserved concurrency** – by default, there are `900` unreserved concurrencies shared across all functions in a region. To prevent other functions from consuming the available concurrent executions, reserve a portion of it to your Lambda function based on the demand of your current workload.
  - **Use exponential backoff in your app** – a technique that uses progressively longer waits between retries for consecutive error responses. This can be used to handle throttling issues by preventing collision between simultaneous requests.
  - **Use a dead-letter queue** – If you’re using Amazon S3 and Amazon EventBridge (Amazon CloudWatch Events), configure your function with a dead letter queue to catch any events that are discarded due to constant throttles. This can protect your data if you’re seeing significant throttling.
  - **Request a service quota increase** – you can reach AWS support to request for a higher service quota for concurrent executions.
- There are two types of authorization available for Lambda function URLs:
  - **AWS_IAM**  – the function URL can only be invoked by an IAM user or role with the necessary permissions. This can be useful in cases where you need to restrict access to the Lambda function to a specific set of users or roles within your organization.
  - **NONE** – anyone can invoke the Lambda function using the URL. This approach can be useful in cases where you want to make the Lambda function publicly accessible and do not require any additional authentication or authorization beyond the URL. However, you may still need to validate the incoming requests in the Lambda function to ensure that the request comes from a trusted source.


## DynamoDB

- Streams records are stored for 24h
- Max 5 LSI and 20 GSI per table
- GSI does not support strongly consistent reads
- Max data of 10GB per partition key value for LSI and table
- `BatchWriteItem` cannot update items, i.e. doesn't support `UpdateItem` operation
- `BatchGetItem` returns failed reads as `UnprocessedKeys`
- DynamoDB has two built-in backup methods (On-demand, Point-in-time recovery) that write to Amazon S3, but you will **NOT** have access to the S3 buckets that are used for these backups.
- In DynamoDB, you can control access to individual data items, indexes and attributes in a table using IAM policy. You use the `IAM Condition` element to implement a fine-grained access control policy.
  - `dynamodb:LeadingKeys` condition key in IAM policy for restricting access to records belonging to specific partition key ex. UserId.
  - `dynamodb:Attributes` condition key in IAM policy for restricting access to attributes.
  - `dynamodb:Select`: for specifying attributes to be returned in the result of a Query or Scan request
  - `dynamodb:ReturnValues`: used for getting the item attributes as they appear before or after they are updated.

## Cognito

- User pool integrates with API Gateway and Application Load Balancer
- When your mobile app authenticates with the Identity Provider (IdP) using Amazon Cognito, the token returned from the IdP is passed to Amazon Cognito, which then returns a **Cognito ID** for the user. This **Cognito ID** is used to provide a set of temporary, limited-privilege AWS credentials through the Cognito Identity Pool.
- With **adaptive authentication**, you can configure your user pool to block suspicious sign-ins or add second factor authentication in response to an increased risk level. For each sign-in attempt, Amazon Cognito generates a risk score for how likely the sign-in request is to be from a compromised source. This risk score is based on factors that include device and user information.
- Amazon Cognito **identity pools** enable you to create unique identities and assign permissions for users. Your identity pool can include:
  - Users in an Amazon Cognito user pool
  - Users who authenticate with external identity providers such as Facebook, Google, or a SAML-based identity provider
  - Users authenticated via your own existing authentication process
- a **user pool** is just a user directory in Amazon Cognito. 

## Step Functions
- Max execution duration
    - Standard - 365d
    - Express - 5min
- Express Workflows do not support activities, job-run (.sync), and Callback patterns.
- Include a `Catch` block in state machine to capture the errors. Then use `ResultPath` block to include each node's input data with its output.

## CodeCommit

- `codecommit:GitPull` and `codecommit:GitPush` IAM permissions are for git client and not for API actions. They are used for git clone, git fetch, git pull and git push.
- You can authenticate with CodeCommit (**HTTPS**) in two ways:
  1. Set-up a **Git credential helper** using your access key credentials specified in your **AWS credential profile**.
  2. Generate HTTPS Git credentials for AWS CodeCommit. Specify the credentials in the **Git Credential Manager**.

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
- AWS KMS establishes quotas for the number of API operations requested in each second. This could lead to throttling of otherwise functional setup.
- When pushing parameters using System Manager Parameter Store, use `ssm:PutParameter` commands.

### Options provided by KMS
- Create symmetric and asymmetric keys where the key material is only ever used within the service
- Create symmetric keys where the key material is generated and used within a custom key store under your control.
- Import your own symmetric key for use within the service.
- Create both symmetric and asymmetric data key pairs for local use within your applications.
- Define which IAM users and roles can manage keys.
- Define which IAM users and roles can use keys to encrypt and decrypt data.
- Choose to have keys that were generated by the service to be automatically rotated on an annual basis.
- Temporarily disable keys so they cannot be used by anyone.
- Re-enable disabled keys.
- Schedule the deletion of keys that you no longer use.
- Audit the use of keys by inspecting logs in `AWS CloudTrail`.

### Symmetric keys vs Asymmetric keys
| Feature          | Symmetric Keys                                                                            | Asymmetric Keys                                                                                                                                                       |
| ---------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Key Structure    | use a single, shared key for both encryption and decryption.                              | consist of a public-private key pair. The public key is used for encryption, and the private key is used for decryption (or vice versa for signing and verification). |
| Key Usage        | Primarily used for encryption and decryption of data.                                     | can be used for encryption/decryption, digital signing, and key agreement (deriving a shared secret)                                                                  |
| Key Distribution | must be kept secret and securely shared between parties that need to encrypt/decrypt data | The public key can be widely distributed, while the private key must be kept secure                                                                                   |
| Performance      | Faster                                                                                    | Slower                                                                                                                                                                |
| Key Rotation     | supports automatic **annual** rotation                                                    | cannot be automatically rotated by KMS.                                                                                                                               |
| Key Import       | can import your own symmetric key material into KMS                                       | cannot import your own asymmetric key material into KMS. Asymmetric keys must be generated by KMS.                                                                    |

## Route 53

- An Alias record can only point to AWS services

## CloudFormation

- Conditions cannot be used in Parameters section
- Exports must have unique names within a single Region
- StackSets are regional resources
- To prevent deletion of successfully provisioned resources on stack failure, use `--disable-rollback` option while creating or updating a stack from CLI

## CDK

- Supported languages - JavaScript/TypeScript, Python, Java, and .NET
- CDK constructs come in three levels of abstraction:
  - **L1 constructs (also called CFN resources)** are low-level constructs that map directly to components that are available in `CloudFormation`. For example, a security group, S3 bucket, or a DynamoDB table.
  - **L2 constructs** provide the same functionality as CloudFormation resources but with added convenience by including the default settings, repetitive code, and connecting logic that you would otherwise have to write with a native CloudFormation resource.
  - **L3 constructs (also called patterns)** define infrastructure that follows best practices and conventions, which can help you improve the structure and maintainability of your CDK apps. For example, you can easily build an API Gateway API backed by a Lambda function using the `aws-apigateway.LambdaRestApi` construct.
- CDK popular commands:
  - `cdk synth`: Prepare an AWS environment for CDK deployments by deploying the CDK bootstrap stack, named CDKToolkit, into the AWS environment.
  - `cdk bootstrap`: Synthesize a CDK app to produce a cloud assembly, including an AWS CloudFormation template for each stack.
  - `cdk context`: Manage cached context values for your CDK application.

## SAM

- To deploy an application that contains one or more nested applications, you must include the `CAPABILITY_AUTO_EXPAND` capability in the sam deploy command.

## Miscellaneous
- If you have resources that are running inside AWS that need programmatic access to various AWS services, then the best practice is always to use **IAM roles**. However, applications running outside of an AWS environment will need access keys for programmatic access to AWS resources. For example, monitoring tools running on-premises and third-party automation tools will need **access keys**.
- You can also provision stack using **CloudFormation** but it only works with **JSON or YAML**. If you want to define your stack in some other language, use **CDK** which works with many familiar languages like Python, Ruby, Java, JavaScript etc.
- Enforce MFA only on users with suspicious login attempts - **Enable Adaptive Authentication for the User Pool.**