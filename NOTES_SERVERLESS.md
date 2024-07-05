# AWS Lambda
AWS Lambda lets you run code as functions without provisioning or managing servers. With serverless computing, your application still runs on servers, but all the server management is done by AWS. 

- Lambda-based applications are composed of `functions` triggered by `events`.
- You cannot log in to the compute instances that run Lambda functions or customize the operating system or language runtime.
- You specify the amount of memory you need allocated to your Lambda functions.
- AWS Lambda allocates CPU power proportional to the memory you specify using the same ratio as a general purpose EC2 instance type.
- Lambda assumes an IAM role when it executes the function.
- AWS Lambda stores code in Amazon S3 and encrypts it at rest.
- Lambda scales concurrently executing functions up to your default limit (1000).

**Lambda functions:**

- Consist of code and any associated dependencies.
- Configuration information is associated with the function.
- You specify the configuration information when you create the function.
- API provided for updating configuration data.
- Functions can access:
    - AWS services or non-AWS services.
    - AWS services running in VPCs (e.g. RedShift, Elasticache, RDS instances).
    - Non-AWS services running on EC2 instances in an AWS VPC.
- There is a maximum execution timeout.
    - Max is 15 minutes (900 seconds), default is 3 seconds.
    - You pay for the time it runs.
    - Lambda terminates the function at the timeout.

# AWS SAM (Serverless Access Model)
A serverless application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include additional resources such as APIs, databases, and event source mappings.

The **AWS Serverless Application Model (AWS SAM)** is an open-source framework designed to streamline the building and deployment of serverless applications on AWS. By simplifying the process of creating and managing resources, it significantly reduces the complexity usually associated with traditional architectures.

![alt text](images/image-sam.png)

## SAM Templates
These are configuration files, written in `YAML` or `JSON`, that specify the resources used in your AWS serverless application. They are an extension of `AWS CloudFormation templates`, providing a simplified syntax for defining serverless resources such as Lambda functions, API Gateway APIs, and DynamoDB tables.

There are several resource types:
```
AWS::Serverless::Function (AWS Lambda)
AWS::Serverless::Api (API Gateway)
AWS::Serverless::SimpleTable (DynamoDB)
AWS::Serverless::Application (AWS Serverless Application Repository)
AWS::Serverless::HttpApi (API Gateway HTTP API)
AWS::Serverless::LayerVersion (Lambda layers)
```

## SAM Command Line Interface (CLI)
The `SAM CLI` is a vital tool for local development and testing of serverless applications. It provides commands for all phases of the development lifecycle, from debugging your application locally to deploying your code in the AWS cloud. Provides a Lambda-like execution environment that lets developers locally build, test, and debug applications defined by SAM templates.

- Offers a shorthand syntax to express functions, APIs, databases, and event source mappings.
- SAM seamlessly transforms and expands this syntax into AWS CloudFormation syntax, facilitating faster and more efficient construction of serverless applications. 
- AWS SAM supports all AWS CloudFormation template items such as `Outputs`, `Mappings`, `Parameters`, providing developers with the comprehensive tooling needed to build robust and scalable serverless applications.

### Commands
```yaml
# Only two commands are required to package and deploy to AWS.

sam package
sam deploy

# OR

aws cloudformation package
aws cloudformation deploy
```

## Key Benefits of AWS SAM

- Works seamlessly with popular integrated development environments (IDEs) like PyCharm, IntelliJ, and VS Code. Also integrates with a comprehensive suite of AWS serverless tools.

- Can leverage AWS CodeBuild, AWS CodeDeploy, and AWS CodePipeline to construct an efficient and reliable deployment pipeline for your serverless applications.

- With SAM CLI, you can locally build, test, and debug applications defined by SAM templates. This accelerates the development cycle by providing immediate feedback. The SAM CLI provides a Lambda-like execution environment that lets developers locally build, test, and debug applications defined by SAM templates.

- AWS SAM is an extension of AWS CloudFormation, which means you can use all the powerful, flexible features of CloudFormation in addition to serverless-specific capabilities.

- AWS SAM templates are designed with best practices in mind, incorporating built-in application lifecycle management features such as safe deployments and rollback capabilities

- AWS SAM enables you to deploy your infrastructure as configuration. This practice facilitates the implementation of additional best practices such as code reviews, which contribute to higher code quality and fewer deployment issues.

## Disadvantages of AWS SAM
- AWS SAM can be restrictive when it comes to API Gateway configuration. While it simplifies many aspects, it doesn’t offer the same level of flexibility as manual or Serverless Framework configurations.

- Compared to the Serverless Framework, AWS SAM has fewer plugins available.
