# Overview
The AWS Application Integration Services are a suite of services that can be used to enable communication between application components in a decoupled architecture. This includes microservices application, distributed systems, and serverless applications.

AWS application integration services allow you to connect apps, without needing to write custom code to enable interoperability. Decoupled applications can interoperate whilst being resilient to the failure or overload of any individual component.

The following services are involved with application integration:

| Service                           | What it does                                                               | Example use cases                                                                                                                                                                         |
| --------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Simple Queue Service (SQS)        | Messaging queue; store and forward patterns                                | Building distributed / decoupled applications                                                                                                                                             |
| Simple Notification Service (SNS) | Set up, operate, and send notifications from the cloud                     | Send email notification when CloudWatch alarm is triggered                                                                                                                                |
| Step Functions                    | Out-of-the-box coordination of AWS service components with visual workflow | Order processing workflow                                                                                                                                                                 |
| Simple Workflow Service (SWF)     | Need to support external processes or specialized execution logic          | Human-enabled workflows like an order fulfilment system or for procedural requests <br> *Note: AWS recommends that for new applications customers consider Step Functions instead of SWF* |
| Amazon MQ                         | Message broker service for Apache Active MQ and RabbitMQ                   | Need a message queue that supports industry standard APIs and protocols; migrate queues to AWS                                                                                            |
| Amazon Kinesis                    | Collect, process, and analyze streaming data.                              | Collect data from IoT devices for later processing                                                                                                                                        |

# AWS SNS

# AWS SQS

# Amazon SWF

# AWS Step Functions