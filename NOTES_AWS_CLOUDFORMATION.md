AWS CloudFormation is a service that allows you to manage, configure and provision your AWS infrastructure as code.

Resources are defined using a CloudFormation template.

CloudFormation has some similarities with `AWS Elastic Beanstalk` though they are also quite different as detailed in the table below:

| CloudFormation                               | Elastic Beanstalk                                                                        |
| -------------------------------------------- | ---------------------------------------------------------------------------------------- |
| “Template-driven provisioning”               | “Web apps made easy”                                                                     |
| Deploys infrastructure using code            | Deploys applications on EC2 (PaaS)                                                       |
| Can be used to deploy almost any AWS service | Deploys web applications based on Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker |
| Uses JSON or YAML template files             | Uses ZIP or WAR files                                                                    |
| Similar to Terraform                         | Similar to Google App Engine                                                             |

# Key Benefits
Infrastructure is provisioned consistently, with fewer mistakes (human error).

Less time and effort than configuring resources manually.

You can use version control and peer review for your CloudFormation templates.

Free to use (you’re only charged for the resources provisioned).

It can be used to manage updates and dependencies.

It can be used to rollback and delete the entire stack as well.

# Key Concepts

The following table describes the key concepts associated with AWS CloudFormation:

| Component   | Description                                                                                                                                                                       |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Templates   | The JSON or YAML text file that contains the instructions for building out the AWS environment                                                                                    |
| Stacks      | The entire environment described by the template and created, updated, and deleted as a single unit                                                                               |
| StackSets   | AWS CloudFormation StackSets extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation |
| Change Sets | A summary of proposed changes to your stack that will allow you to see how those changes might impact your existing resources before implementing them                            |
| Templates   | The JSON or YAML text file that contains the instructions for building out the AWS environment                                                                                    |

# Templates
A template is a `YAML` or `JSON` template used to describe the end-state of the infrastructure you are either provisioning or changing.

After creating the template, you upload it to CloudFormation directly or using Amazon S3.

CloudFormation reads the template and makes the API calls on your behalf.

The resulting resources are called a “Stack”.

Logical IDs are used to reference resources within the template.

Physical IDs identify resources outside of AWS CloudFormation templates, but only after the resources have been created.

## Template elements

**Mandatory:**

```
List of resources and associated configuration values.
```
**Not mandatory:**

```
Template parameters (limited to 60).
Output values (limited to 60).
List of data tables.
```

## Resources

The required `Resources` section declares the AWS resources that you want to include in the stack, such as an Amazon EC2 instance or an Amazon S3 bucket.

- Mandatory.
- Represent AWS components that will be created.
- Resources are declared and can reference each other.

The following example YAML code declares an EC2 instance as a resource:

 ```yaml
 Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: "ami-0ff8a91507f77f867"
 ```

## Parameters

Use the *optional* `Parameters` section to customize your templates. Parameters enable you to input custom values to your template each time you create or update a stack.

- Provide inputs to your CloudFormation template.
- Useful for template reuse.

The following example declares a parameter named `InstanceTypeParameter`. This parameter lets you specify the Amazon EC2 instance type for the stack to use when you create or update the stack.

> Note: the `InstanceTypeParameter` has a default value of `t2.micro`. This is the value that AWS CloudFormation uses to provision the stack unless another value is provided.

```yaml
Parameters: 
  InstanceTypeParameter: 
    Type: String
    Default: t2.micro
    AllowedValues: 
      - t2.micro
      - m1.small
      - m1.large
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.

```

## Pseudo Parameters
Pseudo parameters are parameters that are predefined by AWS CloudFormation. You do not declare them in your template. Use them the same way as you would a parameter, as the argument for the Ref function.

Examples include:

- `AWS::AccountId` – Returns the AWS account ID of the account in which the stack is being created.
- `AWS::NotificationARNs` – Returns the list of notification Amazon Resource Names (ARNs) for the current stack.
- `AWS::Region` – Returns a string representing the AWS Region in which the encompassing resource is being created.
- `AWS::StackId` – Returns the ID of the stack as specified with the aws cloudformation create-stack command.

## Mappings
The *optional* `Mappings` section provides a way to manage and organize different configuration values within a single CloudFormation template, making it easier to maintain and reuse templates across different environments or regions.

Mappings act as lookup tables, allowing you to map a key to a corresponding set of named values.

Mappings are particularly useful when you need to specify different values based on certain conditions, such as the AWS Region, environment (e.g., production or development), or architecture (e.g., 32-bit or 64-bit).

The following example has region keys that associates different Amazon Machine Image (AMI) IDs with specific AWS Regions and different EC2 instance types with different environments.

```yaml
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c94855ba95c71c99
    us-west-1:
      AMI: ami-0e32ec5bc225539f5
  EnvironmentMap:
    Production:
      InstanceType: m5.large
    Development:
      InstanceType: t2.micro
```

To retrieve values from a mapping, you can use the `Fn::FindInMap` intrinsic function within the template's Resources or Outputs sections. Ex.

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      InstanceType: !FindInMap [EnvironmentMap, !Ref EnvironmentType, InstanceType]
```


> **Exam tip:** with mappings you can, for example, set values based on a region. You can create a mapping that uses the region name as a key and contains the values you want to specify for each specific region.

## Outputs
The *optional* `Outputs` section declares output values that you can import into other stacks (to create cross-stack references), return in response (to describe stack calls), or view on the AWS CloudFormation console.

Outputs are useful for exposing information about the resources created by a CloudFormation stack, such as the public IP address of an EC2 instance, the URL of a load balancer, or the ARN of an IAM role. This information can then be used by other stacks or applications that need to interact with the resources created by the stack.

Cannot delete a Stack if its outputs are being referenced by another CloudFormation Stack.

For ex:
```yaml
Outputs:
  InstanceId:
    Description: The instance ID
    Value: !Ref MyEC2Instance
    Export:
      Name: !Sub "${AWS::StackName}-InstanceId"

  WebsiteURL:
    Description: URL for the website
    Value: !Join ["", ["http://", !GetAtt ElasticLoadBalancer.DNSName]]
    Export:
      Name: !Sub "${AWS::StackName}-WebsiteURL"
```

In this example, there are two outputs defined:

- `InstanceId`: This output returns the instance ID of an EC2 instance named `MyEC2Instance`. The `Export` property allows this output value to be imported into other stacks using the `Fn::ImportValue` function.

- `WebsiteURL`: This output constructs a URL by concatenating the string `"http://"` with the DNS name of an Elastic Load Balancing load balancer named `ElasticLoadBalancer`. The `Export` property allows this output value to be imported into other stacks.

You can view the output values of a stack in the AWS CloudFormation console or by using the `aws cloudformation describe-stacks` command in the AWS CLI.

## Conditions
The *optional* `Conditions` section contains statements that define the circumstances under which entities are created or configured.

Applied to `resources` and `outputs`.

In the sample YAML code below, resources are created only if the `EnvType` parameter is equal to prod:

```yaml
Conditions:
  CreateProdResources: !Equals [ !Ref EnvType, prod ]
```

# Transform
The *optional* `Transform` section specifies one or more macros that AWS CloudFormation uses to process your template.

The `Transform` section allows you to use advanced features or syntax in your CloudFormation templates, such as the AWS Serverless Application Model (AWS SAM) or the `AWS::Include` transform.

For example:
- **AWS Serverless Application Model (AWS SAM)**: The `AWS::Serverless` transform allows you to use the AWS SAM syntax in your CloudFormation template to simplify the declaration of AWS Lambda functions, API Gateway APIs, and other serverless resources.

    `Transform: AWS::Serverless-2016-10-31`
- **AWS::Include Transform** : The `AWS::Include` transform allows you to split your CloudFormation template into multiple files and include them in the main template.
    `Transform: AWS::Include`

## Intrinsic Functions
AWS CloudFormation provides several built-in functions that help you manage your stacks. Use intrinsic functions in your templates to assign values to properties that are not available until runtime.

> **Exam tip:** At a minimum, know the intrinsic functions listed below for the exam. The full list can be found at: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html

### Ref

`Fn::Ref (or !Ref in YAML)`

- The intrinsic function `Ref` returns the value of the specified parameter or resource.
- When you specify a parameter’s logical name, it returns the value of the parameter.
- When you specify a resource’s logical name, it returns a value that you can typically use to refer to that resource, such as a physical ID.

### Fn::GetAtt

- The `Fn::GetAtt` intrinsic function returns the value of an attribute from a resource in the template.
- Full syntax (YAML): `Fn::GetAtt: [ logicalNameOfResource, attributeName ]`
- Short form (YAML): `!GetAtt logicalNameOfResource.attributeName`

### Fn::FindInMap

- The intrinsic function `Fn::FindInMap` returns the value corresponding to keys in a two-level map that is declared in the `Mappings` section.
- Full syntax (YAML): `Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]`
- Short form (YAML): `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`

### Fn::ImportValue

- The intrinsic function `Fn::ImportValue` returns the value of an output exported by another stack.
- You typically use this function to create cross-stack references.

### Fn::Join

- To construct a string value
- Full syntax (YAML): `Fn::Join: [ delimiter, [ comma-delimited list of values ] ]`
- Short form (YAML): `!Join [ delimiter, [ comma-delimited list of values ] ]`

### Fn::Sub
- The intrinsic function `Fn::Sub` substitutes variables in an input string with values that you specify.
- In your templates, you can use this function to construct commands or outputs that include values that aren’t available until you create or update a stack.

# Stacks and Stack Sets

## Stacks

Stacks are a fundamental concept in CloudFormation, allowing you to manage and provision AWS resources in a consistent, repeatable, and automated manner. They simplify the process of deploying and maintaining complex infrastructure configurations across multiple AWS services.

Stack is a collection of AWS resources that you can provision and manage as a single unit. A stack is created based on a CloudFormation template.

Logically groups related resources together, making it easier to manage and update them as a single unit.

**Stack creation errors:**

- Automatic rollback on error is enabled by default.
- You will be charged for resources provisioned even if there is an error.

**Updating stacks:**

- AWS CloudFormation provides two methods for updating stacks: direct update or creating and executing change sets.
- When you directly update a stack, you submit changes and AWS CloudFormation immediately deploys them.
- Use direct updates when you want to quickly deploy your updates.
- With change sets, you can preview the changes AWS CloudFormation will make to your stack, and then decide whether to apply those changes.

## Stack Sets

AWS CloudFormation `StackSets` extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation.

Using an `administrator` account, you define and manage an AWS CloudFormation template, and use the template as the basis for provisioning stacks into selected `target` accounts across specified regions.

A stack set is managed by signing in to the AWS `administrator` account in which it was created.

A `target` account is the account into which you create, update, or delete one or more stacks in your stack set.

Before you can use a stack set to create stacks in a target account, you must set up a trust relationship between the administrator and target accounts.

## Nested Stacks

Nested stacks allow re-use of CloudFormation code for common use cases.

For example standard configuration for a load balancer, web server, application server etc.

Instead of copying out the code each time, create a standard template for each common use case and reference from within your CloudFormation template.

## CF helper scripts

AWS CloudFormation provides the following Python helper scripts that you can use to install software and start services on an Amazon EC2 instance that you create as part of your stack:

**cfn-init:** Use to retrieve and interpret resource metadata, install packages, create files, and start services.

**cfn-signal:** Use to signal with a `CreationPolicy` or `WaitCondition`, so you can synchronize other resources in the stack when the prerequisite resource or application is ready.

**cfn-get-metadata:** Use to retrieve metadata for a resource or path to a specific key.

**cfn-hup:** Use to check for updates to metadata and execute custom hooks when changes are detected.

# Best Practices

1. **Use Infrastructure as Code (IaC)** : Treat your infrastructure as code by defining it in CloudFormation templates. This promotes consistency, repeatability, and version control for your infrastructure. [2]

2. **Modular Templates** : Break down your templates into smaller, reusable components or nested stacks. This improves maintainability, readability, and promotes code reuse.

3. **Validate Templates** : Use tools like `cfn-lint` or `taskcat` to validate your CloudFormation templates for syntax errors, best practices, and security issues before deploying them.

4. **Use Change Sets** : Before updating a stack, create and review change sets to preview the changes CloudFormation will make. This helps you identify potential issues before applying the changes.

5. **Implement Stack Policies** : Use stack policies to protect critical resources from unintended updates or deletions, ensuring compliance and preventing accidental changes.

6. **Leverage Parameters and Mappings** : Use parameters and mappings to make your templates more flexible and reusable across different environments or regions.

7. **Avoid Embedding Credentials** : Never embed sensitive information, such as passwords or access keys, directly in your templates. Instead, use AWS Systems Manager Parameter Store or AWS Secrets Manager to securely store and reference sensitive data.

8. **Implement Resource Tagging** : Tag your resources consistently using CloudFormation metadata to improve cost tracking, security, and resource identification.

9. **Enable Termination Protection** : Enable termination protection on critical stacks to prevent accidental deletion of important resources.

10. **Use Version Control** : Store your CloudFormation templates in a version control system (e.g., CodeCommit) to track changes, collaborate, and roll back if needed.

11. **Automate Deployments** : Integrate CloudFormation with CI/CD pipelines to automate the deployment process, ensuring consistent and repeatable infrastructure provisioning.

12. **Test and Stage Changes** : Test your CloudFormation templates in non-production environments before deploying to production to catch and address any issues early.
