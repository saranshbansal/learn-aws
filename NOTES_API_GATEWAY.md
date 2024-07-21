# Amazon API Gateway

![alt text](images/image-api_gw.png)

AWS API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. It acts as a front door for applications to access data, business logic, or functionality from your backend services, including Lambda functions, EC2 instances, or any publicly addressable web service.

API Gateway supports the following:

- Creating, deploying, and managing a REST application programming interface (API) to expose backend HTTP endpoints, AWS Lambda functions, or other AWS services.
- Creating, deploying, and managing a WebSocket API to expose AWS Lambda functions or other AWS services.
- Invoking exposed API methods through the frontend HTTP and WebSocket endpoints.
- Traffic management, authorization and access control, monitoring, and API version management.

Together with Lambda, API Gateway forms the app-facing part of the AWS serverless infrastructure.

## Key Features
The following table describes some of the core features of Amazon API Gateway.

| API Gateway Feature                               | Benefit                                                                                                                                                                                                                              |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Support for RESTful APIs and WebSocket APIs       | With API Gateway, you can create RESTful APIs using either HTTP APIs or REST APIs                                                                                                                                                    |
| Private integrations with AWS ELB & AWS Cloud Map | With API Gateway, you can route requests to private resources in your VPC. Using HTTP APIs, you can build APIs for services behind private ALBs, private NLBs, and IP-based services registered in AWS Cloud Map, such as ECS tasks. |
| Metering                                          | Define plans that meter and restrict third-party developer access to APIs.                                                                                                                                                           |
| Security                                          | API Gateway provides multiple tools to authorize access to APIs and control service operation access.                                                                                                                                |
| Resiliency                                        | Manage traffic with throttling so that backend operations can withstand traffic spikes.                                                                                                                                              |
| Operations Monitoring                             | API Gateway provides a metrics dashboard to monitor calls to services.                                                                                                                                                               |
| Lifecycle Management                              | Operate multiple API versions and multiple stages for each version simultaneously so that existing applications can continue to call previous versions after new API versions are published.                                         |
| AWS Authorization                                 | Support for signature version 4 for REST APIs and WebSocket APIs, IAM access policies, and authorization with bearer tokens (e.g., JWT, SAML) using Lambda functions.                                                                |

## API End Points

![alt text](images/image-api_gw_end_point.png)

## Amazon API Gateway important notes for REST and WebSocket APIs

- API Gateway does not support sharing a custom domain name across REST and WebSocket APIs.

- Stage names can only contain alphanumeric characters, hyphens, and underscores. Maximum length is 128 characters.

- The /ping and /sping paths are reserved for the service health check. Use of these for API root-level resources with custom domains will fail to produce the expected result.

- API Gateway currently limits log events to 1024 bytes. Log events larger than 1024 bytes, such as request and response bodies, will be truncated by API Gateway before submission to CloudWatch Logs.

- CloudWatch Metrics currently limits dimension names and values to 255 valid XML characters. (For more information, see the CloudWatch User Guide.) Dimension values are a function of user-defined names, including API name, label (stage) name, and resource name. When choosing these names, be careful not to exceed CloudWatch Metrics limits.

- The maximum size of a mapping template is 300 KB.

## Stages and Stage variables
A stage is a logical reference to a lifecycle state of your REST or WebSocket API (for example, ‘dev’, ‘prod’, ‘beta’, ‘v2’).

API stages are identified by `API ID` and `stage name`.

![alt text](images/image-api_gw_stages.png)

Stage variables are like environment variables for API Gateway.

Stage variables can be used in:

- Lambda function ARN.
- HTTP endpoint.
- Parameter mapping templates.

Use cases for stage variables:

- Configure HTTP endpoints your stages talk to (dev, test, prod etc.).
- Pass configuration parameters to AWS Lambda through mapping templates.

Stage variables are passed to the “context” object in Lambda.

Stage variables are used with Lambda aliases.

You can create a stage variable to indicate the corresponding Lambda alias.

You can create canary deployments for any stage – choose the % of traffic the canary channel receives.

## Mapping Templates
Mapping templates can be used to: 

- Modify request / responses, Rename parameters.

- Modify body content.

- Add headers.

- Map JSON to XML for sending to backend or back to client.

- Uses Velocity Template Language (VTL).

- Filter output results (remove unnecessary data).

## Custom Domain Names
The default API endpoint name is randomly generated, difficult to recall, and not user-friendly. Custom domain names are simpler and more intuitive URLs that you can provide to your API users.

### Example
After deploying your API, you (and your customers) can invoke the API using the default base URL of the following format:

`https://api-id.execute-api.region.amazonaws.com/stage`

where `api-id` is generated by API Gateway, `region` is the AWS Region, and `stage` is specified by you when deploying the API. The `hostname` portion of the URL, `api-id.execute-api.region.amazonaws.com` refers to an API endpoint. 

With custom domain names, you can set up your API's `hostname`, and choose a base path (for example, `myservice`) to map the alternative URL to your API. For example, a more user-friendly API base URL can become:

`https://api.example.com/myservice`

### Considerations

The following considerations might impact your use of a custom domain name.

- Custom domain names are not supported for private APIs.

- You can disable the default endpoint for your API. Clients can still connect to your default endpoint, but they will receive a 403 Forbidden status code.

- A Regional custom domain name can be can be associated with REST APIs and HTTP APIs. You can use the API Gateway Version 2 APIs to create and manage Regional custom domain names for REST APIs.

- A custom domain name must be unique within a Region across all AWS accounts.

- You can migrate your custom domain name between edge-optimized and Regional endpoints.

- You must create or update your DNS provider's resource record to map to your API endpoint. Without such a mapping, API requests bound for the custom domain name cannot reach API Gateway.

- You can support an almost infinite number of domain names without exceeding the default quota by using a wildcard certificate. For more information, see Wildcard custom domain names.

- You can choose a security policy for your custom domain name. For more information, see Choose a security policy for your REST API custom domain in API Gateway.

- To configure API mappings with multiple levels, you must use a Regional custom domain name and use the TLS 1.2 security policy.

## Identity and Access Management
here are several mechanisms for controlling and managing access to an API.

These mechanisms include:

- Resource-based policies.
- Standard IAM Roles and Policies (identity-based policies).
- IAM Tags.
- Endpoint policies for interface VPC endpoints.
- Lambda authorizers.
- Amazon Cognito user pools.

### IAM `Resource-Based` Policies
Amazon API Gateway resource policies are JSON policy documents that you attach to an API to control whether a specified principal (typically an IAM user or role) can invoke the API.

You can use API Gateway resource policies to allow your API to be securely invoked by:

- Users from a specified AWS account.
- Specified source IP address ranges or CIDR blocks.
- Specified virtual private clouds (VPCs) or VPC endpoints (in any account).
- You can use resource policies for all API endpoint types in API Gateway: private, edge-optimized, and Regional.

### IAM `Identity-Based` Policies
Create IAM policies and attach to users / roles

### Lambda authorizers
Use AWS Lambda to validate the token in the header being passed.

A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity.

When a client makes a request to one of your API's methods, API Gateway calls your Lambda authorizer, which takes the caller's identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

• A `token-based` Lambda authorizer (also called a TOKEN authorizer) receives the caller's identity in a bearer token, such as a `JSON Web Token (JWT)` or an `OAuth` token.

• A `request parameter-based` Lambda authorizer (also called a REQUEST authorizer) receives the caller's identity in a combination of headers, query string parameters, `stageVariables`, and `$context` variables.

Option to cache the result of the authentication.

You pay per Lambda invocation.

Good for using OAuth, SAML or 3rd party authentication.

![alt text](images/image-lambda_authoriser.png)

### Cognito User Pools
Amazon Cognito is a robust AWS service that provides authentication, authorization, and user management for web and mobile applications. A key component of Amazon Cognito is the user pools feature. Here are some notes on Amazon Cognito user pools:

- Sign-up and sign-in services.
- A built-in, customizable web UI to sign in users.
- Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity - providers from your user pool.
- User directory management and user profiles.
- Security features such as multi-factor authentication (MFA).