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
