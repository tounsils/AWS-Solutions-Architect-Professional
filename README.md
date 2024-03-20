# AWS Certified Solutions Architect Professional Cheat Cheets for Senior Engineer

# Practice
* <https://www.examtopics.com/exams/amazon/aws-certified-solutions-architect-professional/view/>
* <https://www.udemy.com/course/aws-solutions-architect-professional-practice-exams-amazon>
* <https://www.udemy.com/course/aws-certified-solutions-architect-professional-aws-practice-exams/>
* <https://app.linuxacademy.com/challenges/7888f62b-4fdb-4a1d-a7c5-a02b46e3280f>

# SLA and Multi-Regions setup
For 2 9s (99%) and 3 9s (99.9%) scenario, a single region setup is enough.

Multi-Regions active-active setup are mandatory for 5 9s (99.999%) or higher

4 9s (99.99%) or 3½ 9s (99.95%) with very short recovery time can be done with only one active region as long as data is available and ressources are ready in an other region.

# VPC
* Creating hundred of VPCs is possible. Usually, creating more AWS accounts is preferred.
* Each time you need to filter access using FQDN, you need a proxy. There is no proxy SaaS in AWS, so you NEED a custom EC2 instance.

## Limits
* Attachments per transit gateway: 5000
* Transit gateways per VPC: 5
* Subnets per VPC: 200, Adjustable
* Active VPC peering connections per VPC : 50, Adjustable (up to 125)

## DNS
* enableDnsHostnames: Indicates whether instances with public IP addresses get corresponding public DNS hostnames.
  - If this attribute is true, instances in the VPC get public DNS hostnames, but only if the enableDnsSupport attribute is also set to true.

* enableDnsSupport: Indicates whether the DNS resolution is supported.
  - If this attribute is false, the Amazon-provided DNS server that resolves public DNS hostnames to IP addresses is not enabled.
  - If this attribute is true, queries to the Amazon provided DNS server at the reserved IP address (.2) will succeed
* It's not possible to modify the IP address range of an existing VPC or subnet. You must delete the VPC or subnet, and then create a new VPC or subnet with your preferred CIDR block.

## Virtual Private Gateway
A logical, fully redundant distributed edge routing function that sits at the edge of your VPC. As it is capable of terminating VPN connections from your on-prem or customer environments, the VPG is the VPN concentrator on the Amazon side of the Site-to-Site VPN connection. Represented the only way to terminate a VPN connection into AWS cloud until the launch of the Transit Gateway.

* Only one virtual private gateway (VGW) can be attached to a VPC.
* Route propagation are enabled on the virtual private gateway (VGW).
* Different from Customer Gateway: a physical device or software appliance on your side of a Site-to-Site VPN connection.
* An Internet gateway is not required to establish a hardware VPN connection.
* Site-to-Site VPN connection support static routing as well as dynamic routing using BGP.

## VPC Endpoints (VPCe)
* Interface endpoints: An elastic network interface with a private IP address that serves as an entry point for traffic destined to services powered by AWS PrivateLink.
  * Link to one or several subnets (for multi-AZ HA)
  * Link to a security group
  * Communicate with the service using an endpoint-specific private DNS hostname.
  * By default, the standard DNS names for SQS and SNS will use the public endpoint and not the interface endpoint. You need to use the private endpoint DNS.

* Gateway endpoints: A gateway that is a target for a specified route in your route table. This type of endpoint is used for traffic destined to a supported AWS service, currently only Amazon S3 or Amazon DynamoDB.

## ELB
* Elastic Load Balancing creates a cookie, named AWSELB, that is used to map the session to the instance.

* AWS Network Load Balancer (NLB) now support Security groups!

* NLB provides an option to add static public IP addresses in each Availability Zone in which the NLB has nodes. Allows to know the public IP addresses that will be returned in DNS resolutions of the NLBs endpoint DNS name. Useful if you're connecting from a company office or data center that restricts outbound communications for certain protocols.

* A NLB should be used for a VPC endpoint service.

## Extension
Amazon Virtual Private Cloud (VPC) now allows customers to expand their VPCs by adding secondary IPv4 address ranges (CIDRs) to their VPCs. Customers can add the secondary CIDR blocks to the VPC directly from the console or by using the CLI after they have created the VPC with the primary CIDR block. Similar to the primary CIDR block, secondary CIDR blocks are also supported by all the AWS services including Elastic Load Balancing and NAT Gateway.

This feature has two key benefits. First, customers, who are launching more and more resources in their VPCs, can now scale up their VPCs on-demand. Second, customers no longer have to over-allocate private IPv4 space to their VPCs - they can allocate only what is required at the time, and later expand it as needed. With these benefits, this feature can make it significantly easier for customers to manage their private IPv4 address space.

## NAT
* NAT Instance - When there is a connection time out, a NAT instance sends a FIN packet to resources behind the NAT instance to close the connection.
* NAT Gateway - When there is a connection time out, a NAT gateway returns an RST packet to any resources behind the NAT gateway that attempt to continue the connection (it does not send a FIN packet).
<https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html>

## Flow logs
Can be published to Amazon S3 (and then be requested by AWS Athena) or CloudWatch Logs
* Traffic Mirroring is an Amazon VPC feature that you can use to copy network traffic from an elastic network interface of Amazon EC2 instances. You can then send the traffic to out-of-band security and monitoring appliances. Useful for Layer 7 scan, as Flow logs are Layer 4 only.

# Direct Connect
You can only create a private virtual interface to a Direct Connect gateway and not a public virtual interface. DX Gateway and Private VIF should be in the same AWS account, whereas the connected VPCs can be in different AWS accounts and regions.

You can use an AWS Direct Connect gateway to connect your AWS Direct Connect connection over a private virtual interface to one or more VPCs in your account that are located in the same or different regions. You associate a Direct Connect gateway with the virtual private gateway for the VPC, and then create a private virtual interface for your AWS Direct Connect connection to the Direct Connect gateway. 

You can attach multiple private virtual interface to your Direct Connect gateway in any VPC, Region, or Account via a **virtual private gateway (GVW)** OR a **Transit Gateway** for multiple VPCs in the same region.

A Direct Connect gateway is a globally available resource. You can create the Direct Connect gateway in any public region and access it from all other public regions.

It allows scaling a Direct Connection to 500 VPCs: a single Direct Connection supports 50 VIFs, a single private VIF can connect to a single Direct Connect Gateway, a single Direct Connect Gateway can connect to 10 VGWs

* The requirements for high throughput, low latency, and high availability require two Direct Connect connections.

* To connect to services such as EC2 using just Direct Connect you need to create a private virtual interface. However, if you want to encrypt the traffic flowing through Direct Connect, you will need to use the public virtual interface of DX to create a VPN connection that will allow access to AWS services such as S3, EC2, and other services.

# Route53
When you create a record, you choose a routing policy, which determines how Amazon Route 53 responds to queries:
* Simple routing policy – Use for a single resource that performs a given function for your domain, for example, a web server that serves content for the example.com website.
* Failover routing policy – Use when you want to configure active-passive failover.
* Geolocation routing policy – Use when you want to route traffic based on the location of your users.
* Geoproximity routing policy – Use when you want to route traffic based on the location of your resources and, optionally, shift traffic from resources in one location to an another.
* Latency routing policy – Use when you have resources in multiple AWS Regions and you want to route traffic to the region that provides the best latency.
* Multivalue answer routing policy – Use when you want Route 53 to respond to DNS queries with up to eight healthy records selected at random. -> Valid answer to increase availability.
* Weighted routing policy – Use to route traffic to multiple resources in proportions that you specify.

To associate an Amazon VPC and a private hosted zone that you created with different AWS accounts: 

1. Using the account that created the hosted zone, authorize the association of the VPC with the private hosted zone by using one of the following methods:
* AWS CLI
* AWSSDK or AWS Tools for Windows PowerShell
* Amazon Route 53 API
* If you want to associate multiple VPCs that you created with one account with a hosted zone that you created with a different account, you must submit one authorization request for each VPC.
* When you authorize the association, you must specify the hosted zone ID, so the private hosted zone must already exist.
2. Using the account that created the VPC, associate the VPC with the hosted zone. As with authorizing the association, you can use the AWS SDK, Tools for Windows PowerShell, the AWS CLI, or the Route 53 API. If you're using the API, use the AssociateVPCWithHostedZone action.
3. (Recommended) Delete the authorization to associate the VPC with the hosted zone. 

* Route 53 Outbound forwarder can be use to forward request to servers inside the VPC.
It's a managed DNS Forwarder.

* Route 53 API requests are limited (by default) to five requests per second per AWS account. If you submit more, Amazon Route 53 returns an HTTP 400 error (Bad request). The response header also includes a "Code element" with a value of "Throttling and" a "Message element" with a value of "Rate exceeded".

# Cloudformation
A cloudformation stack policy is a JSON document that defines the update actions that can be performed on designated resources.

After you set a stack policy, all of the resources in the stack are protected by default. To allow updates on specific resources, you specify an explicit Allow statement for those resources in your stack policy. You can define only one stack policy per stack, but, you can protect multiple resources within a single policy. A stack policy applies to all AWS CloudFormation users who attempt to update the stack. You can't associate different stack policies with different users.

AWS CloudFormation StackSets extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and/or regions with a single operation. Using an administrator account, you define and manage an AWS CloudFormation template, and use the template as the basis for provisioning stacks into selected target accounts across specified regions.

# ECS
Within your container definition, specify secrets with the name of the environment variable to set in the container and the full ARN of either the Secrets Manager secret or Systems Manager Parameter Store parameter containing the sensitive data to present to the container.

Amazon ECS service can optionally be configured to use Service Auto Scaling to adjust its desired count up or down in response to CloudWatch alarms

API Gateway offers a managed API service and can be integrated with ALB and ECS to provide Docker-based microservices.

## ECS Automated Spot Instance Draining
Will automatically place Spot instances in "DRAINING" state upon the receipt of two minute interruption notice. ECS tasks running on Spot instances will automatically be triggered for shutdown before the instance terminates and replacement tasks will be scheduled elsewhere on the cluster. No new ECS service tasks will be started on the instances once the termination process has begun.

ECS takes over the coordination of termination of tasks with the termination of the underlying EC2 instance using the inherent instance "DRAINING" functionality. The managed termination, scheduling of replacement tasks and graceful termination of the LB connections reduces the probability of service interruptions. This makes it easier for customers to use Spot instances as part of their ECS cluster.

## Amazon ECS Anywhere
A new capability in Amazon ECS that enables customers to easily run and manage container-based applications on premises, including virtual machines (VMs), bare metal servers, and other customer-managed infrastructure.

## ECS task execution role
Grants the ECS container and Fargate agents permission to make AWS API calls on your behalf. The task execution IAM role is required depending on the requirements of your task. You can have multiple task execution roles for different purposes and services associated with your account.

According to best practices the permissions that containers (tasks) require should be specified in task execution roles not in container EC2 instance IAM roles.

The following are common use cases for a task execution IAM role:
* pulling a container image from an Amazon ECR private repository (may be in a different account from the account that runs the task)
* access S3 bucket
* send container logs to CloudWatch Logs using the awslogs log driver
* referencing sensitive data using Secrets Manager secrets or AWS Systems Manager Parameter Store parameters

# EC2
Reserved Instances are best to use for these scenarios:
* Applications that have been in use for years and that you plan to continue to use.
* Applications with steady state or predictable usage.
* Applications that require reserved capacity.
* Users who want to make upfront payments to further reduce their total computing costs.

You can use Spot Instances for various fault-tolerant and flexible applications. Examples include web servers, API backends, continuous integration/continuous development, Hadoop data processing, image rendering, big data analytics, and massively parallel computations. Spot Instances are typically used to supplement On-Demand Instances, where appropriate, and are not meant to handle 100% of your workload. However, you can use all Spot Instances for any stateless, non-production application, such as development and test servers, where occasional downtime is acceptable. They are not a good choice for sensitive workloads or databases.

## Scheduled Reserved Instances
Enable you to purchase capacity reservations that recur on a daily, weekly, or monthly basis, with a specified start time and duration, for a one-year term. You reserve the capacity in advance, so that you know it is available when you need it. You pay for the time that the instances are scheduled, even if you do not use them. Scheduled Instances are a good choice for workloads that do not run continuously, but do run on a regular schedule.

* The following are the only supported instance types: C3, C4, M4, and R3.
* The required term is 365 days (one year), minimum required utilization is 1,200 hours per year (3H20 per day).
* You can purchase a Scheduled Instance up to three months in advance.
* They are available in the following Regions: US East (N. Virginia), US West (Oregon), and Europe (Ireland).

## Autoscaling Group
You can remove (detach) an instance from an Auto Scaling group. After the instances are detached, you can manage them independently from the rest of the Auto Scaling group. By detaching an instance you can even move it to a different group. Desired and minimal capacity will be ensured by the scaling group during the migration. The instance will be removed for the ELB and the target group (if any) with draining (if enabled).

-> Termination protection will not prevent an Autoscaling Group from terminating instances, instance protection will.

* detach-load-balancers: Detaches one or more Classic Load Balancers from the specified Auto Scaling group.
This operation detaches only Classic Load Balancers. If you have Application Load Balancers or Network Load Balancers, use DetachLoadBalancerTargetGroups instead.
When you detach a load balancer, it enters the Removing state while deregistering the instances in the group. When all instances are deregistered, then you can no longer describe the load balancer using DescribeLoadBalancers. The instances remain running.

* suspend terminate process: suspend and then resume one or more of the processes for your Auto Scaling group. You might want to do this, for example, so that you can investigate a configuration issue that is causing the process to fail, or to prevent Amazon EC2 Auto Scaling from marking instances unhealthy and replacing them while you are making changes to your Auto Scaling group.

The suspend-resume feature supports the following processes:
* Launch
* Terminate
* AddToLoadBalancer
* AlarmNotification
* AZRebalance
* HealthCheck
* InstanceRefresh
* ReplaceUnhealthy
* ScheduledActions

## Golden AMI
An AMI that you standardize through configuration, consistent security patching, and hardening. It also contains agents you approve for logging, security, performance monitoring. You also need to establish repeatable processes to distribute the golden AMI(s) everywhere, support updates and security for several versions and decommission obsolete AMIs.

## Elastic Fabric Adapter (EFA)
A network interface for Amazon EC2 instances that enables customers to run applications requiring high levels of inter-node communications at scale on AWS. Its custom-built operating system (OS) bypass hardware interface enhances the performance of inter-instance communications, which is critical to scaling these applications. EFA is available as an optional EC2 networking feature that you can enable on any supported EC2 instance at no additional cost.

# S3
## S3 Select
Enables applications to retrieve only a subset of data from an object by using simple SQL expressions. By using S3 Select to retrieve only the data needed by your application, you can achieve drastic performance increases.

## Requester Pays Buckets
In general, bucket owners pay for all Amazon S3 storage and data transfer costs associated with their bucket. A bucket owner, however, can configure a bucket to be a Requester Pays bucket. With Requester Pays buckets, the requester instead of the bucket owner pays the cost of the request and the data download from the bucket. The bucket owner always pays the cost of storing data.

You can host a static website on Amazon S3. On a static website, individual webpages include static content. They might also contain client-side scripts. By contrast, a dynamic website relies on server-side processing, including server-side scripts such as PHP, JSP, or ASP.NET. Amazon S3 does not support server-side scripting.

## S3 versioning
* By default, versioning is disabled.
* Regardless of whether you have enabled versioning, each object in your bucket has a version ID. If you have not enabled versioning, Amazon S3 sets the value of the version ID to null. If you have enabled versioning, Amazon S3 assigns a unique version ID value for the object. When you enable versioning on a bucket, objects already stored in the bucket are unchanged. The version IDs (null), contents, and permissions remain the same.
* When you enable versioning for a bucket, all objects added to it will have a unique version ID. Unique version IDs are randomly generated
* When you PUT an object in a versioning-enabled bucket, the noncurrent version is not overwritten. When a new version of photo.gif is PUT into a bucket that already contains an object with the same name, the original object (ID = 111111) remains in the bucket, Amazon S3 generates a new version ID (121212), and adds the newer version to the bucket.
* When you DELETE an object, all versions remain in the bucket and Amazon S3 inserts a delete marker. The delete marker becomes the current version of the object. By default, GET requests retrieve the most recently stored version. Performing a simple GET Object request when the current version is a delete marker returns a 404 Not Found
* You can GET a noncurrent version of an object by specifying its version ID.
* You can permanently delete an object by specifying the version you want to delete. Only the owner of an Amazon S3 bucket can permanently delete a version.
* You can add additional security by configuring a bucket to enable MFA (multi-factor authentication) Delete. When you do, the bucket owner must include two forms of authentication in any request to delete a version or change the versioning state of the bucket, and of course delete the bucket itself.

## S3 Block Public Access Features:
* IgnorePublicAcls - Causes Amazon S3 to ignore all public ACLs on a bucket and any objects that it contains.
* BlockPublicAcls – PUT bucket ACL and PUT objects requests are blocked if granting public access.
* BlockPublicPolicy – Rejects requests to PUT a bucket policy if granting public access.
* RestrictPublicBuckets – Restricts access to principles in the bucket owners’ AWS account.

## Private restriction
Many companies that distribute content over the internet want to restrict access to documents, business data, media streams, or content that is intended for selected users, for example, users who have paid a fee. To securely serve this private content by using CloudFront, you can do the following:

* Require that your users access your private content by using special CloudFront signed URLs or signed cookies.
* Require that your users access your content by using CloudFront URLs, not URLs that access content directly on the origin server (for example, Amazon S3 or a private HTTP server). Requiring CloudFront URLs isn't necessary, but we recommend it to prevent users from bypassing the restrictions that you specify in signed URLs or signed cookies.

## S3 Object Lock
Can store objects using a write-once-read-many (WORM) model. Object Lock can help prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely. Object Lock help meet regulatory requirements, or to simply add another layer of protection against object changes and deletion. Object Lock works only in versioned buckets.

## Cross-Origin Resource Sharing (CORS)
Cross-Domain Resource Sharing can be enable on an S3 Bucket

## AWS Transfer for SFTP
Enables you to easily move your file transfer workloads that use the Secure Shell File Transfer Protocol (SFTP) to AWS without needing to modify your applications or manage any SFTP servers.
* Create an SFTP server and map your domain to the server endpoint, select authentication for your SFTP clients using service-managed identities, or integrate your own identity provider, and select your Amazon S3 buckets to store the transferred data. Your existing users can continue to operate with their existing SFTP clients or applications. Data uploaded or downloaded using SFTP is available in your Amazon S3 bucket.

## Amazon S3 Transfer Acceleration
Enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket. Transfer Acceleration takes advantage of Amazon CloudFront’s globally distributed edge locations. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path.

## Amazon S3 Multi-Region Access Points
Accelerate content transfers and failover between replicated datasets across AWS Regions. Amazon Simple Storage Service (S3) Multi-Region Access Points provide a global endpoint for routing Amazon S3 request traffic between AWS Regions. Each global endpoint routes Amazon S3 data request traffic from multiple sources.

## Multipart upload
Upload a single object as a set of parts. Each part is a contiguous portion of the object's data. You can upload these object parts independently and in any order. If transmission of any part fails, you can retransmit that part without affecting other parts. After all parts of your object are uploaded, Amazon S3 assembles these parts and creates the object.

In general, when your object size reaches 100 MB, you should consider using multipart uploads instead of uploading the object in a single operation.
* Improved throughput – You can upload parts in parallel.
* Quick recovery from any network issues – Minimizes the impact of restarting a failed upload .
* Pause and resume object uploads – You can upload object parts over time. After you initiate a multipart upload, there is no expiry; you must explicitly complete or stop the multipart upload.
* Begin an upload before you know the final object size – You can upload an object as you are creating it.

## Range HTTP header
In a GET Object request, you can fetch a byte-range from an object, transferring only the specified portion. You can use concurrent connections to Amazon S3 to fetch different byte ranges from within the same object.

This helps you achieve higher aggregate throughput versus a single whole-object request. Fetching smaller ranges of a large object also allows your application to improve retry times when requests are interrupted. Typical sizes for byte-range requests are 8 MB or 16 MB.

## Enforce encryption of data in transit
You can allow only encrypted connections over HTTPS (TLS) by using the *aws:SecureTransport* condition in your Amazon S3 bucket policies. Also consider implementing ongoing detective controls by using the *s3-bucket-ssl-requests-only* managed AWS Config rule.

# AWS Serverless

## EC2 Limit
EC2ThrottledException: To enable your Lambda function to access resources inside your private VPC, you must provide additional VPC-specific configuration information that includes VPC subnet IDs and security group IDs.
AWS Lambda uses this information to set up elastic network interfaces (ENIs) that enable your function to connect securely to other resources within your private VPC.
If your VPC does not have sufficient ENIs or subnet IPs, your Lambda function will not scale as requests increase, and you will see an increase in invocation errors with EC2 error types like EC2ThrottledException.

Using a Lambda in a VPC dramatically reduce its ability to start quickly and its scalability.

## Global limit
Concurrency is the number of in-flight requests your function is handling at the same time. There are two types of concurrency controls available:
* Reserved concurrency – Reserved concurrency is the maximum number of concurrent instances you want to allocate to your function. When a function has reserved concurrency, no other function can use that concurrency. There is no charge for configuring reserved concurrency for a function.
* Provisioned concurrency – Provisioned concurrency is the number of pre-initialized execution environments you want to allocate to your function. These execution environments are prepared to respond immediately to incoming function requests. Configuring provisioned concurrency incurs charges to your AWS account.

By default, Lambda provides your account with a total concurrency limit of 1,000 across all functions in a region. This is a soft limit.

## Batching behavior
Event source mappings read items from a target event source. By default, a single payload is sent to your function. To fine-tune batching behavior, you can configure a batching window (MaximumBatchingWindowInSeconds) and a batch size (BatchSize). A batching window is the maximum amount of time to gather records into a single payload. A batch size is the maximum number of records in a single batch. Lambda invokes your function when one of the following three criteria is met:
* The batching window reaches its maximum value. Batching window behavior varies depending on the specific event source.
   + For Kinesis, DynamoDB, and Amazon SQS event sources: The default batching window is 0 seconds. This means that Lambda sends batches to your function as quickly as possible. If you configure a MaximumBatchingWindowInSeconds, the next batching window begins as soon as the previous function invocation completes.
   + For Amazon MSK, self-managed Apache Kafka, Amazon MQ, and Amazon DocumentDB event sources: The default batching window is 500 ms. You can configure MaximumBatchingWindowInSeconds to any value from 0 seconds to 300 seconds in increments of seconds. A batching window begins as soon as the first record arrives.
* The batch size is met. The minimum batch size is 1. The default and maximum batch size depend on the event source. For details about these values, see the BatchSize specification for the CreateEventSourceMapping API operation.
* The payload size reaches 6 MB. You cannot modify this limit.

## Runtime
[Source](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html)

Objects declared outside of the function's handler method remain initialized, providing additional optimization when the function is invoked again. For example, if your Lambda function establishes a database connection, instead of reestablishing the connection, the original connection is used in subsequent invocations. We recommend adding logic in your code to check if a connection exists before creating a new one.

## Alias routing configuration
Use routing configuration on an alias to send a portion of traffic to a second function version. Configure the alias to send most of the traffic to the existing version, and only a small percentage of traffic to the new version.

You can point an alias to a maximum of two Lambda function versions. The versions must meet the following criteria:
* Both versions must have the same execution role.
* Both versions must have the same dead-letter queue configuration, or no dead-letter queue configuration.
* Both versions must be published. The alias cannot point to $LATEST.

## AWS Serverless Application Repository
The AWS Serverless Application Repository is a managed repository for serverless applications. It enables teams, organizations, and individual developers to store and share reusable applications, and easily assemble and deploy serverless architectures in powerful new ways.

## AWS Serverless Application Model
The AWS Serverless Application Model (AWS SAM) is an open-source framework that you can use to build serverless applications on AWS.
AWS SAM template specification: You use this specification to define your serverless application. It provides you with a simple and clean syntax to describe the functions, APIs, permissions, configurations, and events that make up a serverless application.

AWS SAM is an extension of AWS CloudFormation, you get the reliable deployment capabilities of AWS CloudFormation. You can define resources by using AWS CloudFormation in your AWS SAM template.

## Lambda@Edge
Lambda@Edge is a feature of Amazon CloudFront that lets you run Lambda functions to customize the content that CloudFront delivers, executing the functions in AWS locations closer to the viewer. The functions run in response to CloudFront events, without provisioning or managing servers. You can use Lambda functions to change CloudFront requests and responses

## API Gateway
AWS API Gateway has a max timeout of 29 seconds for all integration types, which includes Lambda as well.
Throttle: a large number of incoming requests will most likely produce an HTTP 502 or 429

### API Gateway Caching
API Gateway caching and CloudFront caching are two powerful and flexible options for caching your API. If you have a small and local user base, with simple and static API content, API Gateway caching may be a good choice for you. If you have a large and global user base, with complex and dynamic API content, CloudFront caching may be a better choice for you. [Source](https://saturncloud.io/blog/api-gateway-caching-vs-cloudfront-which-one-to-use/)

### Direct AWS Service integration with REST
A REST API can integrate directly to several AWS services. An AWS Lambda function is not needed in that case and API Gateway will scale seamlessly. Compatible with Amazon DynamoDB, Kinesis Data Streams, S3, SNS, etc.

* If your Lambda function is not performing custom logic while integrating with other AWS services, chances are that it may be unnecessary. API Gateway, AWS AppSync, Step Functions, EventBridge, and Lambda destinations can directly integrate with a number of services and provide you more value and less operational overhead. Most public serverless applications provide an API with an agnostic implementation of the contract provided, as described in RESTful Microservices. 

### Usage plans and API keys
A usage plan specifies who can access one or more deployed API stages and methods—and optionally sets the target request rate to start throttling requests. The plan uses API keys to identify API clients and who can access the associated API stages for each key.

### API Gateway Lambda proxy integration
A simple, powerful, and nimble mechanism to build an API with a setup of a single API method. The Lambda proxy integration allows the client to call a single Lambda function in the backend. The function accesses many resources or features of other AWS services, including calling other Lambda functions. 

### Integrated mutual TLS authentication
Can be enable on your custom domains to authenticate regional REST and HTTP APIs. You can still authorize requests with bearer or JSON Web Tokens (JWTs) or sign requests with IAM-based authorization.
To use mutual TLS, you upload a CA public key certificate bundle as an object containing public or private/self-signed CA certs. This is used for validation of client certificates. All existing API authorization options are available for use with mTLS authentication. No additional cost.

### Throttling
You can configure throttling and quotas for your APIs to help protect them from being overwhelmed by too many requests. Both throttles and quotas are applied on a best-effort basis and should be thought of as targets rather than guaranteed request ceilings.
API Gateway throttles requests to your API using the token bucket algorithm, where a token counts for a request. Specifically, API Gateway examines the rate and a burst of request submissions against all APIs in your account, per Region.

## Using AWS Lambda with other services
For services that generate a queue or data stream, you create an event source mapping in Lambda and grant Lambda permission to access the other service in the execution role. Lambda reads data from the other service, creates an event, and invokes your function.

Services that Lambda reads events from:
* Amazon Kinesis
* Amazon DynamoDB
* Amazon Simple Queue Service

Other services invoke your function directly. You grant the other service permission in the function's resource-based policy, and configure the other service to generate events and invoke your function. Depending on the service, the invocation can be synchronous or asynchronous. For synchronous invocation, the other service waits for the response from your function and might retry on errors.

Services that invoke Lambda functions synchronously:
* Elastic Load Balancing (Application Load Balancer)
* Amazon Cognito
* Amazon Lex / Amazon Alexa
* Amazon API Gateway
* Amazon CloudFront (Lambda@Edge)
* Amazon Kinesis Data Firehose
* AWS Step Functions
* Amazon Simple Storage Service Batch (S3 Batch)

For asynchronous invocation, Lambda queues the event before passing it to your function. The other service gets a success response as soon as the event is queued and isn't aware of what happens afterwards. If an error occurs, Lambda handles retries, and can send failed events to a dead-letter queue that you configure.

Services that invoke Lambda functions asynchronously:
* Amazon Simple Storage Service (S3)
* Amazon Simple Notification Service (SNS)
* Amazon Simple Email Service (SES)
* AWS CloudFormation
* Amazon CloudWatch Logs / Amazon EventBridge (CloudWatch Events)
* AWS CodeCommit / AWS CodePipeline
* AWS Config
* AWS IoT / AWS IoT Events

# AWS Best practices
## The 6Rs from Discover to AWS (From the fastest to the slowest)
* Rehosting, aka Lift-and-Shift : relatively simple mechanism of copying application and data “bits” from outside AWS, into AWS.
* Replatforming, aka Lift-and-Shape : halfway between Rehosting and Refactoring. It gives some immediate and modest cloud benefits without too much change and risk.
* Repurchasing, aka Drop-and-Shop : eliminate a lot of migration effort and cloud set up by directly consuming applications via the AWS Marketplace.
* Refactoring, aka Cloud Native : transforms everything about the application: it’s components, the application code, and the data itself.
* Remain and Retire : decisions to not migrate an application to the cloud

## The 5 pillars of the AWS Well-Architected Framework
* Operational Excellence:
  - Perform operations as code
  - Make frequent, small, reversible changes
  - Refine operations procedures frequently
  - Anticipate failure and learn from all operational issues

* Security:
  - Implement a strong identity foundation
  - Enable traceability
  - Apply security at all layers
  - Automate security best practices
  - Protect data in transit and at rest
  - Prepare for security events

* Reliability:
  - Scale horizontally to increase aggregate system availability. Replace one large resource with multiple, auto-scaled, small resources to reduce the impact of a single failure on the overall system. Distribute requests across multiple Availability Zones to remove common point of failure.
  - Test recovery procedures
  - Automatically recover from failure
  - Stop guessing capacity
  - Manage change in automation

* Performance Efficiency:
  - Democratize advanced technologies
  - Go global in minutes
  - Use serverless architectures
  - Experiment more often
  - Mechanical sympathy (use a tool or system with an understanding of how it operates best)

* Cost Optimization:
  - Adopt a consumption model
  - Measure overall efficiency (business output of the workload VS costs associated with delivering it)
  - Stop spending money on data center operations
  - Analyze and attribute expenditure (accurately identify the usage and cost of systems, gives workload owners an opportunity to optimize)
  - Use managed services to reduce cost of ownership

## High Availability
Manual recovery < High Availability < Fault Tolerance

## Disaster Recovery (DR) scenarios
* Backup and restore (RPO in hours, RTO in 24 hours or less): Back up your data and applications using point-in-time backups into the DR Region. Restore this data when necessary to recover from a disaster.
* Pilot light (RPO in minutes, RTO in hours): Replicate your data from one region to another and provision a copy of your core workload infrastructure. Resources required to support data replication and backup such as databases and object storage are always on. Other elements such as application servers are loaded with application code and configurations, but are switched off and are only used during testing or when Disaster Recovery failover is invoked.
* Warm standby (RPO in seconds, RTO in minutes): Maintain a scaled-down but fully functional version of your workload always running in the DR Region. Business-critical systems are fully duplicated and are always on, but with a scaled down fleet. When the time comes for recovery, the system is scaled up quickly to handle the production load.
* Multi-region (multi-site) active-active (RPO near zero, RTO potentially zero): Your workload is deployed to, and actively serving traffic from, multiple AWS Regions. This strategy requires you to synchronize data across Regions.

## Deployment
* An in-place upgrade involves performing application updates on live Amazon EC2 instances.
* A disposable upgrade, involves rolling out a new set of EC2 instances by terminating older instances

## OLTP vs OLAP
OnLine Transaction Processing VS OnLine Analytical Processing: In short, Aurora is OLTP whereas Redshift is OLAP

## When Blue/Green Deployments Are Not Recommended
* Are your schema changes too complex to decouple from the code changes? Is sharing of data stores not feasible?
* Does your application need to be "deployment aware"?
* Does your commercial off-the-shelf (COTS) application come with a predefined update/upgrade process that isn’t blue/green deployment friendly?

# AWS Systems Manager
Helps you quickly view operational data for groups of resources, so you can quickly identify any issues that might impact applications that use those resources.

Allows you to automate operational actions to help make your teams more efficient. You can automate maintenance and deployment tasks on Amazon EC2 and on-premises instances, or automatically apply patches, updates, and configuration changes across any resource group.

## Systems Manager Parameter Store
You can use the existing Parameters section of your CloudFormation template to define Systems Manager parameters, along with other parameters. Systems Manager parameters are a unique type that is different from existing parameters because they refer to actual values in the Parameter Store. The value for this type of parameter would be the Systems Manager (SSM) parameter key instead of a string or other value. CloudFormation will fetch values stored against these keys in Systems Manager in your account and use them for the current stack operation.

## AWS Systems Manager Session Manager
Session Manager is a fully managed AWS Systems Manager capability that lets you manage your Amazon EC2 instances through an interactive one-click browser-based shell or through the AWS CLI. Session Manager provides secure and auditable instance management without the need to open inbound ports

## AWS Systems Manager State Manager
Is a secure and scalable configuration management service that automates the process of keeping your Amazon EC2 and hybrid infrastructure in a state that you define.

## AWS Systems Manager Patch Manager
Automates the process of patching managed instances with both security related and other types of updates. You can use Patch Manager to apply patches for both operating systems and applications.

## AWS Systems Manager Automation
Simplifies common maintenance and deployment tasks of Amazon EC2 instances and other AWS resources. Automation enables you to do the following:
* Build Automation workflows to configure and manage instances and AWS resources.
* Create custom workflows or use pre-defined workflows maintained by AWS.
* Receive notifications about Automation tasks and workflows by using Amazon EventBridge (CloudWatch Events).
* Monitor Automation progress and execution details by using the Amazon EC2 or the AWS Systems Manager console.

## AWS Systems Manager Distributor
Helps you package and publish software to AWS Systems Manager managed nodes. You can package and publish your own software or use Distributor to find and publish AWS-provided agent software packages, such as AmazonCloudWatchAgent, or third-party packages such as Trend Micro. Publishing a package advertises specific versions of the package's document to managed nodes that you identify using node IDs, AWS account IDs, tags, or an AWS Region. To get started with Distributor, open the Systems Manager console. In the navigation pane, choose Distributor.

## AWS Systems Manager Maintenance Windows
Helps you define a schedule for when to perform potentially disruptive actions on your nodes such as patching an operating system, updating drivers, or installing software or patches. With Maintenance Windows, you can schedule actions on numerous other AWS resource types, such as Amazon Simple Storage Service (Amazon S3) buckets, Amazon Simple Queue Service (Amazon SQS) queues, AWS Key Management Service (AWS KMS) keys, and many more. Each maintenance window has a schedule, a maximum duration, a set of registered targets (the nodes or other AWS resources that are acted upon), and a set of registered tasks.

## Unified Cloudwatch Agent
Centralized logs from EC2 instance, replace the old SSM Agent. The batch_count parameter specifies the max number of log events in a batch, up to 10000. Using a value of 1 will result in every log entry being immediately streamed to CloudWatch Logs. This is the most reliable method of collecting the logs as by using a streaming solution the logs are immediately saved.

## SSM Agent
Software that can be installed and configured on an Amazon EC2 instance, an on-premises server, or a virtual machine. SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources.

Run command: securely and remotely manage the configuration of your Amazon ECS container instances. Run Command provides a simple way to perform common administrative tasks without logging on locally to the instance. You can manage configuration changes across your clusters by simultaneously executing commands on multiple container instances. Run Command reports the status and results of each command.

# AWS Cloudwatch
Amazon CloudWatch now includes cross-account cross-region dashboards, which enable you to create high level operational dashboards, and with one click, drill down into more specific dashboards in different AWS accounts without having to log in and out of different accounts or switch AWS Regions.

## Amazon EventBridge
EventBridge is a serverless service that uses events to connect application components together, making it easier for you to build scalable event-driven applications. Event-driven architecture is a style of building loosely-coupled software systems that work together by emitting and responding to events. Event-driven architecture can help you boost agility and build reliable, scalable applications.

Use EventBridge to route events from sources such as home-grown applications, AWS services, and third-party software to consumer applications across your organization. EventBridge provides simple and consistent ways to ingest, filter, transform, and deliver events so you can build applications quickly.

EventBridge was formerly called Amazon CloudWatch Events. The default event bus and the rules you created in CloudWatch Events also display in the EventBridge console. EventBridge uses the same CloudWatch Events API, so your code that uses the CloudWatch Events API stays the same.

## Cloudwatch alarm versus Cloudwatch Event
* Events can self-trigger based on a schedule; alarms don't do this
* Alarms invoke actions only for sustained changes
* Alarms watch one or several metric(s) and respond to changes in that/these metric(s); events can respond to actions (such as a lambda being created or some other change in your AWS environment)
* Alarms can be added to CloudWatch dashboards, but events cannot
* Events are processed by targets, with many more options than the actions an alarm can trigger

# AWS Config
A service that enables you to assess, audit, and evaluate the configurations of your AWS resources.
Continuously monitors and records your AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations.

## Multi-Account Multi-Region Data Aggregation
An aggregator is an AWS Config resource type that collects AWS Config configuration and compliance data from the following:
* Multiple accounts and multiple regions.
* Single account and multiple regions.
* An organization in AWS Organizations and all the accounts in that organization.

## Automatic remediation (with SSM)
Noncompliant resources that are evaluated by AWS Config Rules can be fixed automatically. AWS Config applies remediation using AWS Systems Manager Automation documents. These documents define the actions to be performed on noncompliant AWS resources evaluated by AWS Config Rules.
AWS Config provides a set of managed automation documents with remediation actions. You can also create and associate custom automation documents with AWS Config rules.

# IAM
* Service-linked role: a unique type of IAM role that is linked directly to an AWS service. Service-linked roles are predefined by the service and include all the permissions that the service requires to call other AWS services on your behalf.
* audit access: Do not give Example Corp access to an IAM user and its long-term credentials in your AWS account. Instead, use an IAM role and its temporary security credentials.
* external ID: Use it in the following situations:
  - You are an AWS account owner and you have configured a role for a third party that accesses other AWS accounts in addition to yours.
  - You should ask the third party for an external ID that it includes when it assumes your role.
  - Then you check for that external ID in your role's trust policy.
  - Doing so ensures that the external party can assume your role only when it is acting on your behalf.

* An IAM role is similar to an IAM user, in that it is an AWS identity with permission policies that determine what the identity can and cannot do in AWS. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it. Also, a role does not have standard long-term credentials such as a password or access keys associated with it. Instead, when you assume a role, it provides you with temporary security credentials for your role session.

* Power user access: Provides full access to AWS services and resources, but does not allow management of Users and groups.
The first statement of this policy uses the NotAction element to allow all actions for all AWS services and for all resources except AWS Identity and Access Management and AWS Organizations. The second statement grants IAM permissions to create a service-linked role. This is required by some services that must access resources in another service, such as an Amazon S3 bucket. It also grants Organizations permissions to view information about the user's organization, including the master account email and organization limitations.
* AdministratorAccess:
  - Use case: This user has full access and can delegate permissions to every service and resource in AWS.
  - Policy description: This policy grants all actions for all AWS services and for all resources in the account.
* SystemAdministrator: This user sets up and maintains resources for development operations.

* Access control lists (ACLs) are service policies that allow you to control which principals in another account can access a resource. ACLs cannot be used to control access for a principal within the same account. ACLs are similar to resource-based policies, although they are the only policy type that does not use the JSON policy document format. Amazon S3, AWS WAF, and Amazon VPC are examples of services that support ACLs.

* Resource-based policies are JSON policy documents that you attach to a resource such as an Amazon S3 bucket. These policies grant the specified principal permission to perform specific actions on that resource and defines under what conditions this applies.

* Identity-based policies are JSON permissions policy documents that you can attach to an identity (user, group of users, or role). These policies control what actions an entity (user or role) can perform, on which resources, and under what conditions.

* The IAM service supports only one type of resource-based policy called a role trust policy, which is attached to an IAM role. An IAM role is both an identity and a resource that supports resource-based policies. For that reason, you must attach both a trust policy and an identity-based policy to an IAM role. Trust policies define which principal entities (accounts, users, roles, and federated users) can assume the role.

* There is no such thing as an IAM bucket policy as only S3 has this kind of policy

* It's a best practice that you upload SSL certificates to AWS Certificate Manager (ACM). If you're using certificate algorithms and key sizes that aren't currently supported by ACM or the associated AWS resources, then you can also upload an SSL certificate to IAM using AWS Command Line Interface (AWS CLI).

* An IAM user cannot be added to an IAM group in a different account.

* A Permissions boundary for IAM entities (users or roles) is an advanced feature for using a managed policy to set the maximum permissions that an identity-based policy can grant to an IAM entity. An entity's permissions boundary allows it to perform only the actions that are allowed by both its identity-based policies and its permissions boundaries. You can use an AWS managed policy or a customer managed policy to set the boundary for an IAM entity (user or role). That policy limits the maximum permissions for the user or role.

## Providing Access to an IAM User in Another AWS Account That You Own
* In the development account:
  * Create a development IAM group with the ability to create and delete application infrastructure.
  * Create an operations IAM group with the ability to assume the shared role in the production account.
  * Create an IAM user for each developer and assign them to the development group.
  * Create an IAM user for each operator and assign them to the development group and the operations group.
* In the production account:
  * Create a shared IAM role with the ability to create and delete application infrastructure.
  * Add the development account to the trust policy for the shared role.

# AWS Certificate Manager (ACM)
A service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with AWS services and your internal connected resources. With AWS Certificate Manager, you can generate public or private SSL/TLS certificates that you can use to secure your site.
* Public SSL/TLS certificates provisioned through AWS Certificate Manager are free.
* You pay only for the AWS resources you create to run your application.
* For private certificates, the ACM Private Certificate Authority (CA) is priced along two dimensions: (1) You pay a monthly fee for the operation of each private CA until you delete it and (2) you pay for the private certificates you issue each month.
* Public certificates generated from ACM can be used on Amazon CloudFront, Elastic Load Balancing, or Amazon API Gateway but not directly on EC2 instances unlike private certificates.
* Public certificates identify resources on the public Internet, whereas private certificates do the same for private networks. One key difference is that applications and browsers trust public certificates automatically by default, whereas an administrator must explicitly configure applications to trust private certificates.

# AWS Shield
Managed Distributed Denial of Service (DDoS) protection service that safeguards applications running on AWS. AWS Shield provides always-on detection and automatic inline mitigations that minimize application downtime and latency, so there is no need to engage AWS Support to benefit from DDoS protection. There are two tiers of AWS Shield - Standard and Advanced.
* All AWS customers benefit from the automatic protections of AWS Shield Standard, at no additional charge.
* AWS Shield Advanced provides additional detection and mitigation against large and sophisticated DDoS attacks
* AWS Shield Standard can mitigate Layer 3 or Layer 4 attacks, but it does not include a detailed notification of the recent layer attacks to your AWS resources such as SYN floods and UDP reflection attacks.

-> Cloudfront, AWS WAF and Route 53 can also protect dynamic web applications against DDoS attacks.

# AWS WAF
* Traffic is filter before cloudfront, an API Gateway or an ELB.
* Conditions: check about a network traffic.
* Rules: combine conditions + rate rule (if necessary)
* WebACL: rule and actions

AWS Managed Rules for AWS WAF is a managed service that provides protection against common application vulnerabilities or other unwanted traffic. You have the option of selecting one or more rule groups from AWS Managed Rules for each web ACL, up to the maximum web ACL capacity unit (WCU) limit. 
You can subscribe to managed rules and pay only for what you use, without having to sign up for any expensive professional services. Managed rules are automatically updated, and there are no contracts or subscription commitments. Managed rules are charged by the hour.

# AWS CloudTrail
A service that enables governance, compliance, operational auditing, and risk auditing of your AWS account.
You have to configure Cloudtrail to get information from multiple regions and global services (cloudfront, IAM). By default, it doesn't!

Best practices:
* Send cloud trail logs in a single location (S3 Bucket) in an other account
* Enable MFA delete on the bucket and ensure no unauthorized user have access to it
* Enable CloudTrail log file integrity validation: This feature ensure a log file wasn't modified, deleted, or forged after CloudTrail delivered it. Built using SHA-256 for hashing and SHA-256 with RSA for digital signing. You can use the AWS CLI to validate the files in the location where CloudTrail delivered them.

## AWS CloudTrail Lake (Organization trails)
A fully managed, central query mechanism for CloudTrail events across accounts and regions. Lets organizations aggregate, immutably store, and query events recorded by CloudTrail for auditing, security investigation, and operational troubleshooting. This new platform simplifies CloudTrail analysis workflows by integrating collection, storage, preparation, and optimization for analysis and query in the same product. This removes the need to maintain separate data processing pipelines that span across teams and products to analyze CloudTrail events.

# Amazon Macie
A security service that uses machine learning to automatically discover, classify, and protect sensitive data in AWS. Amazon Macie recognizes sensitive data such as personally identifiable information (PII) or intellectual property, and provides you with dashboards and alerts that give visibility into how this data is being accessed or moved.

# AWS CloudHSM
The web server uses a public-private key pair and an SSL/TLS public key certificate to establish an HTTPS session with each client. This process involves a lot of computation for the web server, but you can offload some of this to the HSMs in your AWS CloudHSM cluster. This is sometimes known as SSL acceleration. HSM can Offload traffic for ELB. HSM generate and manage keys for you using dedicated hardware equipment.

CloudHSM let you generate your own keys, without any link to AWS. These keys are more secure than the customer managed keys you can create in AWS KMS.

You can place the HSMs in different Availability Zones in an AWS region. Adding more HSMs to a cluster provides higher performance. Spreading clusters across Availability Zones provides redundancy and high availability.

The first time setup for M of N authentication involves creating and registering a key for signing and setting the minimum value on the HSM. This involves the following high-level steps:
* To use quorum authentication, each CO must create an asymmetric key for signing (a signing key). This is done outside of the HSM. Keys can be personal keys or public keys.
* A CO must log in to the HSM and then set the quorum minimum value, also known as the m value. This is the minimum number of CO approvals that are required to perform HSM user management operations. Any CO on the HSM can set the quorum minimum value, including COs that have not registered a key for signing.

* If you use your own on-premise HSM (custom key stores), AWS KMS does not support the following features:
  + Automatic key rotation
  + Multi-Region keys

# Authentication
## STS
STS generates temporary credentials, not temporary users. Using the TVM model would allow you to deliver credentials to the user that can grant access to additional services, where as the "Signature Version 4" model would only allow your application to upload to S3.
* TVM is a single point of failure (yes if EC2 instance, but can also be deployed on Elastic Beanstalk)
* A TVM is just a piece of code, AWS suggest 2 java implementation (Anonymous TVM and Identity TVM)
* TVM is old school and should be replaced by Amazon Cognito
* It's possible to revoke STS credentials via the IAM dashboard and going on the role used to generate the creds.

## AssumeRoleWithWebIdentity
Returns a set of temporary security credentials for users who have been authenticated in a mobile or web application with a web identity provider. Example providers include Amazon Cognito, Login with Amazon, Facebook, Google, or any OpenID Connect-compatible identity provider.
* You can use a role to configure your SAML 2.0-compliant identity provider (IdP) and AWS to permit your federated users to access the AWS Management Console.
* The role grants the user permissions to carry out tasks in the console (like adfs portal at Polyconseil).
* This specific use of SAML differs from the more general one illustrated at "About SAML 2.0-based Federation" because this workflow opens the AWS Management Console on behalf of the user.
* This requires the use of the AWS SSO (now IAM Identity Center) endpoint instead of directly calling the AssumeRoleWithSAML API.

## AssumeRoleWithSAML
Is used with STS to get temporary security credentials.

Federation needs management from a single account and role switching (sts:Assumerole) is used to move between accounts.

## Identity Broker
If your identity store is not compatible with SAML 2.0, then you can build a custom identity broker application to perform a similar function. The broker application authenticates users, requests temporary credentials for users from AWS, and then provides them to the user to access AWS resources.

To enable corporate employees to access the company's AWS resources, you can develop a custom identity broker application. The application verifies that employees are signed into the existing identity and authentication system of the company (which might use LDAP, Active Directory, or another system). The identity broker application then obtains temporary security credentials for the employees. To get temporary security credentials, the identity broker application calls either the AssumeRole or GetFederationToken actions in STS to obtain temporary security credentials.

## Amazon Cognito
In many cases obviates the need for implementing a token vending machine as it is capable of (with the help of IAM and STS) vending credentials directly to your device. It has additional benefits of offering data synchronization of small amounts of user data across devices.
* Supports MFA
* Data at-rest and in-transit encryption
* Log in via social identity providers
* Support for SAML

### Amazon Cognito User Pools
A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers. Whether your users sign in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).

Signed users receive authentication tokens. Tokens can be exchanged for AWS access via Amazon Cognito identity pools.

### Amazon Cognito Identity Pools (or Federated Identities)
Amazon Cognito identity pools provide temporary AWS credentials for users who are guests (unauthenticated) and for users who have been authenticated and received a token. An identity pool is a store of user identity data specific to your account.

Authenticates users with web identity providers, including Amazon Cognito user pools. Assigns temporary AWS credentials via AWS STS.

* User pools are for authentication (identify verification). With a user pool, your app users can sign in through the user pool or federate through a third-party identity provider (IdP).
* Identity pools are for authorization (access control). You can use identity pools to create unique identities for users and give them access to other AWS services.

## AWS IAM Identity Center (Successor to AWS Single Sign-On)
Makes it easy to centrally manage access to multiple AWS accounts and business applications and provide users with single sign-on access to all their assigned accounts and applications from one place. IAM Identity Center is the recommended approach for workforce authentication and authorization on AWS for organizations of any size and type. Using IAM Identity Center, you can create and manage user identities in AWS, or connect your existing identity source, including Microsoft Active Directory, Okta, Ping Identity, JumpCloud, Google Workspace, and Azure Active Directory (Azure AD).

* supports single sign-on to business applications through web browsers only, not intended to give access to mobile application.
* supports only SAML 2.0–based applications so an OpenID Connect-compatible solution will not work for this scenario.
* SAML based identity providers should be centralized in one account, not mapped to each individual account.

## AWS Directory Service
Lets you run Microsoft Active Directory (AD) as a managed service. AWS Directory Service for Microsoft Active Directory, also referred to as AWS Managed Microsoft AD, is powered by Windows Server 2012 R2. You can also configure a trust relationship between AWS Managed Microsoft AD in the AWS Cloud and your existing on-premises Microsoft Active Directory, providing users and groups with access to resources in either domain, using single sign-on (SSO).

# Amazon Cloudfront
CloudFront assigns a default domain name to your distribution, for example, d111111abcdef8.cloudfront.net. If you use this domain name, then you can use the CloudFront default SSL/TLS certificate already selected for your distribution.

## CloudFront RTMP
RTMP distributions stream media files using Adobe Media Server and the Adobe Real-Time Messaging Protocol (RTMP)
Soon to be deprecated (December 31, 2020)

## Access restriction
To restrict access to content that you serve from Amazon S3 buckets, you create CloudFront signed URLs or signed cookies to limit access to files in your Amazon S3 bucket, and then you create a special CloudFront user called an origin access identity (OAI) and associate it with your distribution. Then you configure permissions so that CloudFront can use the OAI to access and serve files to your users, but users can't use a direct URL to the S3 bucket to access a file there.

### Origin Access Control (OAC)
While OAI provides a secure way to access S3 origins to CloudFront, it has limitations such as not supporting granular policy configurations, HTTP and HTTPS requests that use the POST method in AWS regions that require AWS Signature Version 4 (SigV4), or integrating with SSE-KMS. To strengthen security and deepen feature integrations, today, we are introducing origin access control (OAC), a new feature that secures S3 origins by permitting access to the designated distributions only. OAC is based on an AWS best practice of using IAM service principals to authenticate with S3 origins. Compared with OAI, some of the notable enhancements OAC provide include:

* Security – OAC is implemented with enhanced security practices like short term credentials, frequent credential rotations, and resource-based policies. They strengthen your distributions’ security posture and provides better protections against attacks like confused deputy.
* Comprehensive HTTP methods support – OAC supports GET, PUT, POST, PATCH, DELETE, OPTIONS, and HEAD.
* SSE-KMS – OAC supports downloading and uploading S3 objects encrypted with SSE-KMS.
* Access S3 in all AWS regions – OAC supports accessing S3 in all AWS regions, including existing regions and all future regions. In contrast, OAI will only be supported in existing AWS regions and regions launched before December 2022.

### URL Signing
Both S3 and CloudFront have URL signing features that work differently.
However, only S3 refers to them as Pre-signed URLs CloudFront refers to them as Signed URLs and Signed Cookies.

CloudFront signed cookies allow you to control who can access your content when you don't want to change your current URLs or when you want to provide access to multiple restricted files, for example, all of the files in the subscribers' area of a website.

All objects and buckets by default are private. The presigned URLs are useful if you want your user/customer to be able to upload a specific object to your bucket, but you don't require them to have AWS security credentials or permissions.

In CloudFront, a signed URL allow access to a path. Therefore, if the user has a valid signature, he can access it, no matter the origin.

In S3, a signed URL issue a request as the signer user. When you sign a request, you need to provide IAM credentials, so accessing a signed URL has the same effect as that user would have done it.

To create signed URLs or signed cookies, you need at least one AWS account that has an active CloudFront key pair. This account is known as a trusted signer. The trusted signer has two purposes:

* As soon as you add the AWS account ID for your trusted signer to your distribution, CloudFront starts to require that users use signed URLs or signed cookies to access your files.

* When you create signed URLs or signed cookies, you use the private key from the trusted signer's key pair to sign a portion of the URL or the cookie. When someone requests a restricted file, CloudFront compares the signed portion of the URL or cookie with the unsigned portion to verify that the URL or cookie hasn't been tampered with. CloudFront also verifies that the URL or cookie is valid, meaning, for example, that the expiration date and time hasn't passed.

## HTTP Headers for SSE-C encryption
For Amazon S3 REST API calls, you have to include the following HTTP Request Headers:
* x-amz-server-side-encryption-customer-algorithm
* x-amz-server-side-encryption-customer-key
* x-amz-server-side-encryption-customer-key-MD5

For presigned URLs, you should specify the algorithm using the request header:
* x-amz-server-side-encryption-customer-algorithm

## Origin Protocol Policy
* HTTP Only: CloudFront uses only HTTP to access the origin.
* HTTPS Only: CloudFront uses only HTTPS to access the origin.
* Match Viewer: CloudFront communicates with your origin using HTTP or HTTPS, depending on the protocol of the viewer request. CloudFront caches the object only once even if viewers make requests using both HTTP and HTTPS protocols.

## Viewer Protocol Policy
* HTTP and HTTPS: Viewers can use both protocols.
* Redirect HTTP to HTTPS: Viewers can use both protocols, but HTTP requests are automatically redirected to HTTPS requests.
* HTTPS Only: Viewers can only access your content if they're using HTTPS.

## Certificates
To use an ACM certificate with Amazon CloudFront, you must request the certificate in the US East (N. Virginia) region. ACM certificates in this region that are associated with a CloudFront distribution are distributed to all the geographic locations configured for that distribution.
* Cloudfront can use dedicated IP address to serve HTTPS requests for client that are not compatible with SNI.

## AWS-managed prefix list
Limit the inbound HTTP/HTTPS traffic to your origins from only the IP addresses that belong to CloudFront’s origin-facing servers. CloudFront keeps the managed prefix list up-to-date with the IP addresses of CloudFront’s origin-facing servers, so you no longer have to maintain a prefix list yourself.

# AWS Organizations
An organization functionalities are determined by the feature set that you enable: "All features" or "Consolidated Billing only"

After you create an organization and verify that you own the email address associated with the master account, you can invite existing AWS accounts to join your organization. When you invite an account, AWS Organizations sends an invitation to the account owner, who decides whether to accept or decline the invitation.

## Service Control Policies
* SCPs affect all users and roles in attached accounts, including the root user.
* The AWS Organizations master account isn't affected by any SCP. You can't limit what users and roles in the master account can do by applying SCPs. SCPs affect only member accounts.
* SCPs do not affect any service-linked role.
* SCPs affect only principals that are managed by accounts that are part of the organization.
* Any action that has an explicit Deny in an SCP can't be delegated to users or roles in the affected accounts. An explicit Deny statement overrides any Allow that other SCPs might grant.
* Any action that has an explicit Allow in an SCP (such as the default "*" SCP or by any other SCP that calls out a specific service or action) can be delegated to users and roles in the affected accounts.
* Any action that isn't explicitly allowed by an SCP is implicitly denied and can't be delegated to users or roles in the affected accounts.

By default, an SCP named FullAWSAccess is attached to every root, OU, and account. This default SCP allows all actions and all services. So in a new organization, until you start creating or manipulating the SCPs, all of your existing IAM permissions continue to operate as they did. As soon as you apply a new or modified SCP to a root or OU that contains an account, the permissions that your users have in that account become filtered by the SCP. Permissions that used to work might now be denied if they're not allowed by the SCP at every level of the hierarchy down to the specified account.

* SCP determines what services and actions can be delegated by administrators to the users and roles in the accounts that the SCP is applied to. It does not grant any permissions
* SCPs are similar to IAM permission policies except that they don't grant any permissions.

* SCP can be use to force tag in every resources (using ForAllValues and aws:TagKeys)

* As the master account cannot be impacted by SCPs, that account should be avoided for normal usage.

## AWS Resource Access Manager (AWS RAM)
Lets you share your resources with any AWS account or through AWS Organizations. If you have multiple AWS accounts, you can create resources centrally and use AWS RAM to share those resources with other accounts.

* Owners can share non-default subnets only with other accounts or organizational units that are in the same organization from AWS Organizations.

* Enable resource sharing within your organization in Organizations. This operation creates a service-linked role called AWSServiceRoleForResourceAccessManager that has the IAM managed policy named AWSResourceAccessManagerServiceRolePolicy attached. This role permits RAM to retrieve information about the organization and its structure. This lets you share resources with all of the accounts in the calling account's organization by specifying the organization ID, or all of the accounts in an organizational unit (OU) by specifying the OU ID. AWS RAM doesn't send invitations to principals. Principals in your organization gain access to shared resources without exchanging invitations.

# EBS
EBS volume types (price per GB-month):
* gp2: $0.10 - General purpose SSD volume that balances price and performance for a wide variety of workloads
  + IOPS per volume: Minimum of 100 IOPS (at 33.33 GiB and below), maximum of 16000 (at 5,334 GiB and above).
  + Ability to burst to 3,000 IOPS (useless starting at 1TB).
  + Throughput limit: 128 MiB/s if < 170 GiB, 250 MiB/s if burst credits are available and if > 170 GiB but < 334 GiB, 250 MiB/s if > 334 GiB.
* gp3 : $0.08 - Latest generation of General Purpose SSD volumes, and the lowest cost SSD volume offered by Amazon EBS
  + 3,000 IOPS - $0.005/provisioned IOPS-month over 3,000 (maximum of 16,000)
  + 125 MB/s - $0.040/provisioned MB/s-month over 125 (maximum of 1,000 MiB/s)
  + no minimum size required for this performance
* io1: $0.125 + IOPS-month - Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads, random I/O
  + Max IOPS per volume: 64000
  + Max throughput per volume: 1000 MiB/s
* io2: $0.125 + IOPS-month - Highest performance and highest durability SSD volume designed for latency-sensitive transactional workloads
  + Max IOPS per volume: 64000
  + Max throughput per volume: 1000 MiB/s
  + Durability: 99.999% (instead of 99.8% - 99.9% for other types)
* st1: $0.045 - Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads, sequential I/O (not bootable)
  + Max throughput per volume: 500 MiB/s
  + Ability to burst to 500 MiB/s starting at 2TB (useless starting at 12,5TB)
  + Min Volume size: 500 GiB
* sc1: $0.025 - Lowest cost HDD volume designed for less frequently accessed workloads, cold storage (not bootable)
  + Max throughput per volume: 250 MiB/s (only during burst)
  + Min Volume size: 500 GiB

* Volume size, type and throughput can now be modified while attached to an instance
* Max EBS volume size: 16TB (64TB only with io2 Block Express)

* EBS snapshots are incremental backups.
* Snapshots of encrypted volumes are automatically encrypted.
* Volumes that you create from encrypted snapshots are automatically encrypted.
* Volumes that you create from an unencrypted snapshot that you own or have access to can be encrypted on-the-fly.

* You can use EBS direct APIs to create EBS snapshots, write data directly to your snapshots, read data on your snapshots, and identify the differences or changes between two snapshots.

* Amazon EBS Multi-Attach enables you to attach a single Provisioned IOPS SSD (io1 or io2) volume to multiple instances that are in the same Availability Zone.
  + You can attach multiple Multi-Attach enabled volumes to an instance or set of instances.
  + Each instance to which the volume is attached has full read and write permission to the shared volume.
  + Multi-Attach makes it easier for you to achieve higher application availability in clustered Linux applications that manage concurrent write operations.


# Snowball
* Standard Snowball (50 and 80TB): use case: transfer data to S3.
  - Not available anymore, deprecated
* Snowball edge is a autonomous computing device with ability to run EC2 instances and Lambda.
  - The Storage Optimized option provides up to 100 TB of storage (80 TB really usable), 24 vCPUs, and 1 TB SSD for pre-processing and large scale data transfer.
  - New option available with 210 TB SSD
  - The Compute Optimized option provides up to 52 (now 104) vCPUs, an optional GPU, 7.68 TB SSD, and 42 TB of storage for running advanced machine learning workloads in environments with limited or no network connectivity.
* Snowmobile: 100PB

* Snowball Edge is suitable for:
  * Import data into Amazon S3
  * Export from Amazon S3
  * Durable local storage
  * Local compute with AWS Lambda
  * Local compute instances
  * Use in a cluster of devices
  * Use with AWS Greengrass (IoT)
  * Transfer files through NFS with a GUI

* The file interface exposes a Network File System (NFS) mount point for each bucket on your AWS Snowball Edge device. You can mount the file share from your NFS client using standard Linux, Microsoft Windows, or macOS commands. You can use standard file operations to access the file share.
* We recommend that you use only one method at a time of reading and writing data to each bucket on a Snowball Edge device. Using both the file interface and the Amazon S3 adapter on the same bucket might result in undefined behavior.
* Amazon S3 adapter, use to transfer data programmatically to and from the AWS Snowball Edge device using Amazon S3 REST API actions. This Amazon S3 REST API support is limited to a subset of actions.

## Transfer Performance
If you are using a Snowball Edge with the S3 data transfer mechanism, the data transfer rate using the file interface is typically between 25 MB/s and 40 MB/s. If you need to transfer data faster than this, use the NFS adapter. The NFS adapter has a data transfer rate typically between 250 MB/s and 400 MB/s.
* Perform multiple write operations at one time
* Transfer small files in archive
* Don't perform other operations on files during transfer
* Reduce local network use
* Eliminate unnecessary hops and use linux

# AWS Storage Gateway
## File Gateway
A file gateway supports a file interface into Amazon Simple Storage Service (Amazon S3) and combines a service and a virtual software appliance. By using this combination, you can store and retrieve objects in Amazon S3 using industry-standard file protocols such as Network File System (NFS) and Server Message Block (SMB).

## Volume Gateway
A volume gateway provides cloud-backed storage volumes that you can mount as Internet Small Computer System Interface (iSCSI) devices from your on-premises application servers.
* Volume Gateway - Cached volumes: You store your data in Amazon Simple Storage Service (Amazon S3) and retain a copy of frequently accessed data subsets locally.
* Volume Gateway - Stored volumes: If you need low-latency access to your entire dataset, first configure your on-premises gateway to store all your data locally. Then asynchronously back up point-in-time snapshots of this data to Amazon S3.
* Maximum size of a volume: Cached volumes: 32TB - Stored Volumes: 16TB
* Maximum number of volumes per gateway: 32
* Total size of all volumes: Cached volumes: 1PB - Stored Volumes: 512TB

## Tape Gateway
Also named Virtual Tape Library (VTL), allow you to cost-effectively and durably archive backup data in GLACIER or DEEP_ARCHIVE
* Maximum size of a virtual tape: 15TB
* Maximum number of virtual tapes for a VTL: 1500
* Total size of all tapes in a VTL: 1 PB

To restore a Storage Gateway volume in case of DR, generate an EBS volume from the gateway and attach it to the server

## Gateway-Virtual Tape Library (Gateway-VTL)
A service that integrates your on-premises IT environment with AWS storage. With Gateway-VTL, AWS Storage Gateway now provides you with a cost-effective, scalable, and durable virtual tape infrastructure that allows you to eliminate the challenges associated with owning and operating an on-premises physical tape infrastructure.

With Gateway-VTL you can have a limitless collection of virtual tapes. Each virtual tape can be stored in a Virtual Tape Library backed by Amazon S3 or a Virtual Tape Shelf backed by Amazon Glacier.

## Authentication CHAP
AWS Storage Gateway supports authentication between your gateway and iSCSI initiators by using Challenge-Handshake Authentication Protocol (CHAP). CHAP provides protection against playback attacks by periodically verifying the identity of an iSCSI initiator as authenticated to access a volume and VTL device target.

# Amazon EFS
Is a file storage service for use with Amazon EC2. Amazon EFS provides a file system interface, file system access semantics (such as strong consistency and file locking), and concurrently-accessible storage for up to thousands of Amazon EC2 instances.

EFS provides the same level of high availability and high scalability like S3 however, this service is more suitable for scenarios where it is required to have a POSIX-compatible file system or if you are storing rapidly changing data.

EFS replication can create a replica of your Amazon EFS file system in the AWS Region of your preference. When you enable replication, Amazon EFS automatically and transparently replicates the data and metadata on the source file system to a new destination EFS file system. To manage the process of creating the destination file system and keeping it synced with the source file system, Amazon EFS uses a replication configuration. Amazon EFS replication is continual and designed to provide a recovery point objective (RPO) and a recovery time objective (RTO) of minutes.

## Infrequent Access (IA) storage classes
EFS offers two types of storage—standard storage that uses multiple Availability Zones (AZ) to provide high availability and durability, and One Zone storage that provides continuous availability within one zone.

The service offers a storage class for frequently-accessed files and a separate class that offers reduced pricing for infrequently-accessed files. Pricing is based on the volume of storage, the level of redundancy (one zone vs multiple zones), and the storage tier (GB transferred are priced for IA tier).
You can also optimize pricing using features such as bursting throughput, provisioned throughput, and EFS lifecycle management.
* Enable Amazon EFS Lifecycle Management for your file system by selecting a lifecycle policy that matches your needs. Amazon EFS will automatically and transparently move your files to the lower cost regional EFS Standard-IA storage class or EFS One Zone-IA storage class based on the last time they were accessed.

# Migration Services
## AWS Server Migration Service (SMS) (Deprecated)
An agentless service which can migrate thousands of on-premises workloads to AWS. Allows you to automate, schedule, and track incremental replications of live server volumes (up to 16 TB). You can migrate virtual machines from VMware vSphere and Windows Hyper-V to AWS

## AWS Application Migration Service (AWS MGN)
Is the primary migration service recommended for lift-and-shift migrations to AWS.Minimizes time-intensive, error-prone manual processes by automating the conversion of your source servers to run natively on AWS. It also simplifies application modernization with built-in and custom optimization options. AWS MGN supports both agent-based and agentless snapshot approaches to replicate servers from on-premises to AWS.

## AWS Database Migration Service (DMS)
Helps you migrate databases to AWS quickly and securely. The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database. The AWS Database Migration Service can migrate your data to and from most widely used commercial and open-source databases.

Supports homogeneous migrations as well as heterogeneous migrations between different database platforms

You can create an AWS DMS task that captures ongoing changes to the source data store. You can do this capture while you are migrating your data. You can also create a task that captures ongoing changes after you complete your initial (full-load) migration to a supported target data store. This process is called ongoing replication or change data capture (CDC). AWS DMS uses this process when replicating ongoing changes from a source data store. This process works by collecting changes to the database logs using the database engine's native API.

## AWS Schema Conversion Tool (AWS SCT)
Convert your existing database schema from one database engine to another.
Provides a project-based user interface to automatically convert the database schema of your source database into a format compatible with your target Amazon RDS instance.

When you use AWS SCT and an AWS Snowball Edge device, you migrate your data in several stages:
* First, you use the AWS SCT to process the data locally and then move that data to the AWS Snowball Edge device.
* You then send the device to AWS using the AWS Snowball Edge process, and then AWS automatically loads the data into an Amazon S3 bucket.
* Next, when the data is available on Amazon S3, you use AWS SCT to migrate the data to Amazon Redshift. 

In some migration scenarios, the source and target databases are very different from one another and require additional data transformation.
* AWS Schema Conversion Tool (AWS SCT) is extensible, so that you can address these scenarios using an agent.
* An agent is an external program that's integrated with AWS SCT, but performs data transformation elsewhere (such as on an Amazon EC2 instance).
* In addition, an AWS SCT agent can interact with other AWS services on your behalf, such as creating and managing AWS Database Migration Service tasks for you.
* Data extraction agents can work in the background while AWS SCT is closed. You manage your extraction agents by using AWS SCT. The extraction agents act as listeners. When they receive instructions from AWS SCT, they extract data from your data warehouse.

## AWS Application Discovery Service
Collects and presents configuration, usage, and behavior data from your servers to help you better understand your workloads. Provides utilization data and dependency mapping.

Discover on-premises server inventory and behavior to plan cloud migrations:
* Agentless discovery can be performed by deploying the Application Discovery Service Agentless Collector (Agentless Collector) (OVA file) through your VMware vCenter. After Agentless Collector is configured, it identifies virtual machines (VMs) and hosts associated with vCenter. Agentless Collector collects the following static configuration data: Server hostnames, IP addresses, MAC addresses, disk resource allocations, database engine versions, and database schemas. Additionally, it collects the utilization data for each VM and database providing the average and peak utilization for metrics such as CPU, RAM, and Disk I/O.
* Agent-based discovery can be performed by deploying the AWS Application Discovery Agent on each of your VMs (like Hyper-V) and physical servers. The agent installer is available for Windows and Linux operating systems. It collects static configuration data, detailed time-series system-performance information, inbound and outbound network connections, and processes that are running.

## AWS Migration Hub
Provides a single place to discover your existing servers, plan migrations, and track the status of each application migration. The Migration Hub provides visibility into your application portfolio and streamlines planning and tracking. You can visualize the connections and the status of the servers and databases that make up each of the applications you are migrating, regardless of which migration tool you are using.

Migration Hub gives you the choice to start migrating right away and group servers while migration is underway, or to first discover servers and then group them into applications. Migration Hub supports migration status updates from AWS Application Migration Service (ex SMS) and AWS Database Migration Service (AWS DMS).

## AWS Management Portal for vCenter Server
Enables you to manage your AWS resources using VMware vCenter. The portal installs as a vCenter Server plug-in within your existing vCenter Server environment. Once installed, it enables you to migrate VMware VMs to Amazon EC2 and manage AWS resources from within vCenter Server.

## Server Migration Connector on VMware
AWS SMS to migrate VMs from VMware to Amazon EC2

## AWS Cloud Adoption Readiness Tool (CART)
Helps organizations of all sizes develop efficient and effective plans for cloud adoption and enterprise cloud migrations. This 16-question online survey and assessment report details your cloud migration readiness across six perspectives including business, people, process, platform, operations, and security.

## AWS DataSync
A secure, online service that automates and accelerates moving data between on premises, other public clouds and AWS Storage services. DataSync can copy data between Network File System (NFS) shares, Server Message Block (SMB) shares, Hadoop Distributed File Systems (HDFS), self-managed object storage, AWS Snowcone, Amazon Simple Storage Service (Amazon S3) buckets, Amazon Elastic File System (Amazon EFS) file systems, Amazon FSx for Windows File Server file systems, Amazon FSx for Lustre file systems, Amazon FSz for OpenZFS file systems, and Amazon FSx for NetApp ONTAP file systems.

# Elasticache
By default, the data in a Redis node on ElastiCache resides only in memory and is not persistent. If a node is rebooted, or if the underlying physical server experiences a hardware failure, the data in the cache is lost.
If you require data durability, you can enable the Redis append-only file feature (AOF). When this feature is enabled, the node writes all of the commands that change cache data to an append-only file. When a node is rebooted and the cache engine starts, the AOF is "replayed"; the result is a warm Redis cache with all of the data intact.

## Disaster recovery or fault tolerance
* Multi-AZ with Automatic Failover is the best option when data retention, minimal downtime, and application performance are a priority.
    Data loss potential - Low. Multi-AZ provides fault tolerance for every scenario, including hardware-related issues.
    Performance impact - Low. Of the available options, Multi-AZ provides the fastest time to recovery, because there is no manual procedure to follow after the process is implemented.
    Cost - Low to high. Use Multi-AZ when you can't risk losing data because of hardware failure or you can't afford the downtime required by other options in your response to an outage.

* Daily automatic backups
You can schedule daily automatic backups at a time when you expect low resource utilization for your cluster. ElastiCache creates a backup of the cluster, and then writes all data from the cache to a Redis RDB file. Redis versions 2.8.22 and later implement a forkless backup that can improve performance.
    Data loss potential - High (up to a day’s worth). Daily automatic backups are retained for up to 35 days.
    Performance impact - Medium to high. Running multiple file backups throughout the day impacts performance. To improve performance, enable RDB snapshots on a designated persistence only secondary node.

* Manual backups using Redis append-only file (AOF)
Retained indefinitely and are useful for testing and archiving. You can schedule manual backups to occur up to 20 times per node within any 24-hour period.
This is a suitable option for maintaining a high level of data persistence at a relatively low cost by using the functionality that is native to Redis versions 2.8.21 and earlier.

## Caching Strategies
* Lazy Loading: Loads data into the cache only when necessary
  - Only requested data is cached
  - New empty node aren't fatal for your application
  - Cache miss penalty on read
  - Stale data: need Write-Through or TTL
* Write-Through: Adds data or updates data in the cache whenever data is written to the database
  - Data in the cache is never stale
  - Write penalty, but less read penalty, improve read performance
  - Missing data on new node
  - Cache churn: most data is never read, which is a waste of resources, need TTL
* Adding TTL: Lazy loading allows for stale data but doesn't fail with empty nodes. Write-through ensures that data is always fresh, but can fail with empty nodes and can populate the cache with superfluous data. By adding a time to live (TTL) value to each write, you can have the advantages of each strategy. At the same time, you can and largely avoid cluttering up the cache with extra data.

## Memcached
You can choose Memcached over Redis if you have the following requirements:
* You need the simplest model possible.
* You don't need encryption
* You need to run large nodes with multiple cores or threads.
* You need the ability to scale out and in, adding and removing nodes as demand on your system increases and decreases.

In terms of commands execution, Redis is mostly a single-threaded server. It is not designed to benefit from multiple CPU cores unlike Memcached, however, you can launch several Redis instances to scale out on several cores if needed.

# RDS
Amazon RDS Oracle doesn't support the following Oracle Database features:
* Automatic Storage Management (ASM)
* Database Vault
* Flashback Database
* Multitenant
* Oracle Enterprise Manager Cloud Control Management Repository
* Real Application Clusters (Oracle RAC)
* Real Application Testing
* Unified Auditing, Pure Mode

You can use the Amazon RDS package rdsadmin.rdsadmin_rman_util to perform RMAN backups of your Amazon RDS for Oracle database to disk. The rdsadmin.rdsadmin_rman_util package supports full and incremental database file backups, tablespace backups, and archive log backups. Amazon Relational Database (RDS) for Oracle supports integration with Amazon Simple Storage Service (S3) for data ingress and egress capabilities. This feature allows RDS Oracle customers to easily, efficiently and securely transfer data between their RDS Oracle DB Instances and Amazon S3.

It's possible to replicate an MySQL RDS to an external MySQL Instance. And vice-versa. Use the read-replica.

It's possible to have Cross-Region Replication for the following engines: MariaDB, MySQL, Oracle Enterprise Edition (with limitations) and PostgreSQL.
* Aurora is also compatible.
* A source DB instance can have cross-Region read replicas in multiple AWS Regions.
* You can only create a cross-Region Amazon RDS read replica from a source Amazon RDS DB instance that is not a read replica of another Amazon RDS DB instance.
* For MariaDB, MySQL, and Oracle DB instances, when the source for a cross-Region read replica is deleted, the read replica is promoted.
* For PostgreSQL DB instances, when the source for a cross-Region read replica is deleted, the replication status of the read replica is set to terminated. The read replica isn't promoted.

**Amazon Aurora Global Database** is designed for globally distributed applications, allowing a single Amazon Aurora database to span multiple AWS Regions. It replicates your data with no impact on database performance, enables **fast** local reads with **low latency** in each Region, and provides disaster recovery from Region-wide outages.

Aurora Global Database uses storage-based replication with typical latency of less than 1 second, using dedicated infrastructure that leaves your database fully available to serve application workloads. In the unlikely event of a Regional degradation or outage, one of the secondary Regions can be promoted to read and write capabilities in less than 1 minute.

**Amazon Aurora Multi-Master** allow multiple read-write instances of your Aurora database across multiple Availability Zones, which enables uptime-sensitive applications to achieve continuous write availability through instance failure.
All DB instances in a multi-master cluster must be in the same AWS Region and you can't enable cross-region replicas from multi-master clusters.

## RDS Proxy
Amazon RDS Proxy is a fully managed, highly available database proxy for Amazon Relational Database Service (RDS) that makes applications more scalable, more resilient to database failures, and more secure.

Many applications may open and close database connections at a high rate, exhausting database memory and compute resources. Amazon RDS Proxy allows applications to pool and share connections established with the database.

With RDS Proxy, failover times for Aurora and RDS databases are reduced by up to 66% and database credentials, authentication, and access can be managed through integration with AWS Secrets Manager and AWS Identity and Access Management (IAM).

By default, the endpoint that you connect to when you use RDS Proxy with an Aurora cluster has read/write capability. As a result, this endpoint sends all requests to the writer instance of the cluster. All of those connections count against the max_connections value for the writer instance. If your proxy is associated with an Aurora DB cluster, you can create additional read/write or read-only endpoints for that proxy.

You can use a read-only endpoint with your proxy for read-only queries. You do this the same way that you use the reader endpoint for an Aurora provisioned cluster. Doing so helps you to take advantage of the read scalability of an Aurora cluster with one or more reader DB instances. You can run more simultaneous queries and make more simultaneous connections by using a read-only endpoint and adding more reader DB instances to your Aurora cluster as needed.

## Limitations of encrypted DB instances

* You can only encrypt an Amazon RDS DB instance when you create it, not after the DB instance is created. However, because you can encrypt a copy of an unencrypted snapshot, you can effectively add encryption to an unencrypted DB instance by restoring a DB instance from the encrypted snapshot.

* You can't turn off encryption on an encrypted DB instance.

* You can't unencrypt an encrypted DB instance. However, you can export data from an encrypted DB instance and import the data into an unencrypted DB instance.

* You can't create an encrypted snapshot of an unencrypted DB instance.

* A snapshot of an encrypted DB instance must be encrypted using the same KMS key as the DB instance.

* You can't have an encrypted read replica of an unencrypted DB instance or an unencrypted read replica of an encrypted DB instance.

* Encrypted read replicas must be encrypted with the same KMS key as the source DB instance when both are in the same AWS Region.

* You can't restore an unencrypted backup or snapshot to an encrypted DB instance.

* To copy an encrypted snapshot from one AWS Region to another, you must specify the KMS key in the destination AWS Region. This is because KMS keys are specific to the AWS Region that they are created in.

* The source snapshot remains encrypted throughout the copy process. Amazon RDS uses envelope encryption to protect data during the copy process.


# DynamoDB
## Global Tables
A fully managed, multi-region, and multi-master database that provides fast, local, read and write performance for massively scaled, global applications.
Configure the architecture before inserting any data, because you can add replicas only if the table is empty.

## Streams
DynamoDB captures a time-ordered sequence of item-level modifications in any DynamoDB table, and stores this information in a log for up to 24 hours.
Streams can contains only the key of the modified item, the old data, the new data, or both the old and the new.

You can then use this stream to solve the following use-cases:
* An application in one AWS Region modifies the data in a DynamoDB table. A second application in another Region reads these data modifications and writes the data to another table, creating a replica that stays in sync with the original table. You can also create a multi-master topology, storing data in multiple AWS Regions, keeping masters in sync by consuming and replaying the changes.
* A popular mobile app modifies data in a DynamoDB table, at the rate of thousands of updates per second. Another application captures and stores data about these updates, providing near-real-time usage metrics for the mobile app.
* An application automatically sends notifications to the mobile devices of all friends in a group as soon as one friend uploads a new picture.

Amazon DynamoDB is integrated with AWS Lambda so that you can create triggers—pieces of code that automatically respond to events in DynamoDB Streams. With triggers, you can build applications that react to data modifications in DynamoDB tables.
If you enable DynamoDB Streams on a table, you can associate the stream Amazon Resource Name (ARN) with an AWS Lambda function that you write. Immediately after an item in the table is modified, a new record appears in the table's stream. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records.

If asked, choose to decouple the application tier with SQS in front of DynamoDB.

## Amazon DynamoDB Accelerator (DAX)
A fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement. Does all the heavy lifting required to add in-memory acceleration to your DynamoDB tables, without requiring developers to manage cache invalidation, data population, or cluster management.

-> Increase READ performance
-> For WRITE performance, use SQS as buffer or increase WCU

## Conditional Writes
By default, the DynamoDB write operations (PutItem, UpdateItem, DeleteItem) are unconditional: Each operation overwrites an existing item that has the specified primary key.

DynamoDB optionally supports conditional writes for these operations. A conditional write succeeds only if the item attributes meet one or more expected conditions. Otherwise, it returns an error. Conditional writes are helpful in many situations. For example, you might want a PutItem operation to succeed only if there is not already an item with the same primary key. Or you could prevent an UpdateItem operation from modifying an item if one of its attributes has a certain value.

Conditional writes are helpful in cases where multiple users attempt to modify the same item.

## Primary keys
* Partition key – A simple primary key, composed of one attribute known as the partition key. DynamoDB uses the partition key's value as input to an internal hash function. The output from the hash function determines the partition (physical storage internal to DynamoDB) in which the item will be stored.

* Partition key and sort key – Referred to as a composite primary key, this type of key is composed of two attributes. The first attribute is the partition key, and the second attribute is the sort key. DynamoDB uses the partition key value as input to an internal hash function. The output from the hash function determines the partition (physical storage internal to DynamoDB) in which the item will be stored.

Take note that the partition key of an item is also known as its hash attribute. The term hash attribute derives from the use of an internal hash function in DynamoDB that evenly distributes data items across partitions, based on their partition key values.

* To better accommodate uneven access patterns, DynamoDB **adaptive capacity** enables your application to continue reading and writing to hot partitions without being throttled, provided that traffic does not exceed your table’s total provisioned capacity or the partition maximum capacity. Adaptive capacity works by automatically and instantly increasing throughput capacity for partitions that receive more traffic.

## GSI vs LSI (Global and Local Secondary Index)
* LSI has the same partition key as the table, but with different sort key. A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value. As a result, the total size of indexed items for any one partition key value can't exceed 10 GB. Also, a local secondary index shares provisioned throughput settings for read and write activity with the table it is indexing.
* GSI has different partition key from the table. A global secondary index has no size limitations and has its own provisioned throughput settings for read and write activity that are separate from those of the table.

* LSI are created at the same time that you create a table. You cannot add a local secondary index to an existing table, nor can you delete any local secondary indexes that currently exist.
* A GSI allows for alternative PK and SORT keys but only one of each. A single GSI cannot allow multiple different sets of keys.
* Create 2 x GSIs with alternative primary and sort key values, allowing QUERY operations to occur efficiently.
* Each table in DynamoDB is limited to 20 global secondary indexes (default limit) and 5 local secondary indexes.
* <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html>

## Dedicated table
* RCU is allocated by table
* Allow selection of a more efficient table structure
* By isolating the data, the per-item size is reduced, meaning the app would read less data per lookup.
* Performance is limited per partition, so you need to distribute your data with the right attribute to prevent hot-partition issues.
Nowadays, DynamoDB allow a specific partition to borrow RCU and WCU from other less used partitions (but it remain under the hard limit of 3000 RCU / 1000 WCU)

## Permissions
You can have fine-grained policies on DynamoDB table, to allow edition of specific attribut by, for example, federated users.

## Scaling
With a manually set scaling policy, you can set the upper and low limits of the scaling. You could set it at a minimum of 1 and a max of 20 read capacity. This would mean that no matter what you would always have at least 1 read capacity but never more than 20. During a spike, DynamoDB will throttle those requests and prevent you from going over your max auto scaled threshold.

With On-Demand Scaling you don't need to think about provisioning throughput. Instead, your table will scale all behind the scenes automatically. Extreme spikes in load can occur and be handled seamlessly by AWS. This also brings a change in the cost structure for DynamoDB. With On-Demand Scaling, instead of paying for provisioned throughput, you pay per read or write request.

For Provisioned with Auto-Scaling you are basically paying for throughput 24/7. Whereas for On-Demand Scaling you pay per request. This means for applications still in development or low traffic applications, it might be more economical to use On-Demand Scaling and not worry about provisioning throughput. However, at scale, this can quickly shift once you have a more consistent usage pattern.

You can scale DynamoDB tables and global secondary indexes using target tracking scaling policies and scheduled scaling.

# Amazon Kinesis
## Amazon Kinesis Data Streams (KDS)
A Massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. The data collected is available in milliseconds to enable real-time analytics use cases such as real-time dashboards, real-time anomaly detection, dynamic pricing, and more.

* Amazon Kinesis Data Streams synchronously replicates data across three availability zones, providing high availability and data durability.
* A single shard can ingest up to 1 MB of data per second (including partition keys) or 1,000 records per second for writes.
* The minimum value of a data stream's retention period is 24 hours. The maximum value of a stream's retention period is 8760 hours (365 days).
* An Amazon Kinesis Data Streams producer is an application that puts user data records into a Kinesis data stream (also called data ingestion).
* The Kinesis Producer Library (KPL) simplifies producer application development, allowing developers to achieve high write throughput to a Kinesis data stream.

## Kinesis Client Library
KCL helps you consume and process data from a Kinesis data stream by taking care of many of the complex tasks associated with distributed computing. These include load balancing across multiple consumer application instances, responding to consumer application instance failures, checkpointing processed records, and reacting to resharding. The KCL takes care of all of these subtasks so that you can focus your efforts on writing your custom record-processing logic.

## Amazon Kinesis Data Firehose
The easiest way to reliably load streaming data into data lakes, data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today. -> Higher Latency (60sec)

* The maximum size of a record sent to Kinesis Data Firehose, before base64-encoding, is 1,000 KiB.
* Firehose does not use Kinesis clients; it loads data directly to a destination.

## Amazon Kinesis Connector Library
This is a pre-built library that helps you easily integrate Amazon Kinesis Data Streams with other AWS services and third-party tools. Amazon Kinesis Client Library (KCL) is required for using this library. The current version of this library provides connectors to Amazon DynamoDB, Amazon Redshift, Amazon S3, and Elasticsearch.

## Amazon Kinesis Video Streams
Amazon Kinesis Video Streams makes it easy to securely stream video from connected devices to AWS for analytics, machine learning (ML), playback, and other processing. Kinesis Video Streams automatically provisions and elastically scales all the infrastructure needed to ingest streaming video data from millions of devices. It durably stores, encrypts, and indexes video data in your streams, and allows you to access your data through easy-to-use APIs.

# Big Data
## Amazon EMR
Amazon EMR provides a managed Hadoop framework that makes it easy, fast, and cost-effective to process vast amounts of data across dynamically scalable Amazon EC2 instances.

Other distributed frameworks such as Apache Spark, HBase, Presto, and Flink in EMR are available. Can interact with data in AWS data stores such as Amazon S3 and Amazon DynamoDB.

## Type of nodes
* Master Node: manages the cluster and typically runs master components of distributed applications. For example, the master node runs the YARN ResourceManager service to manage resources for applications, as well as the HDFS NameNode service. It also tracks the status of jobs submitted to the cluster and monitors the health of the instance groups.
* Core nodes: managed by the master node. Core nodes run the Data Node daemon to coordinate data storage as part of the Hadoop Distributed File System (HDFS). They also run the Task Tracker daemon. Unlike the master node, there can be multiple core nodes. Removing HDFS daemons from a running core node or terminating core nodes risks data loss. Use caution when configuring core nodes to use Spot Instances.
* Task nodes: (optional) used them to add power to perform parallel computation tasks on data, such as Hadoop MapReduce tasks and Spark executors. Task nodes don't run the Data Node daemon, nor do they store data in HDFS.

## Amazon Redshift
Amazon Redshift workload management (WLM) enables users to flexibly manage priorities within workloads so that short, fast-running queries won't get stuck in queues behind long-running queries.

Increasing the value of wlm_query_slot_count limits the number of concurrent queries that can be run. For example, suppose the service class has a concurrency level of 5 and wlm_query_slot_count is set to 3. While a query is running within the session with wlm_query_slot_count set to 3, a maximum of 2 more concurrent queries can be executed within the same service class. Subsequent queries wait in the queue until currently executing queries complete and slots are freed.

### Cross-Region Snapshots
Automatically backed up your data to a second AWS Region. Once enable, Amazon Redshift will automatically, continuously and incrementally, backup your cluster to two AWS Regions, giving you additional options for business continuity. You can choose the retention period for automated snapshots. Compatible with KMS-encrypted clusters. To copy snapshots for AWS KMS–encrypted clusters to another AWS Region, create a grant for Amazon Redshift to use a KMS customer master key (CMK) in the destination AWS Region. Then choose that grant when you enable copying of snapshots in the source AWS Region.

## AWS Data Pipeline
A web service that helps you reliably process and move data between different AWS compute and storage services, as well as on-premises data sources, at specified intervals. With AWS Data Pipeline, you can regularly access your data where it’s stored, transform and process it at scale, and efficiently transfer the results to AWS services such as Amazon S3, Amazon RDS, Amazon DynamoDB, and Amazon EMR.

## Amazon QuickSight
Business intelligence service that makes it easy to deliver insights to everyone in your organization.
* Fully managed service which lets you easily create and publish interactive dashboards that include ML Insights.
* Dashboards can then be accessed from any device, and embedded into your applications, portals, and websites.
* Pay-per-Session pricing

# OpsWorks
## Goal
AWS OpsWorks is an application management service that makes it easy to deploy and operate applications of all types and sizes. You can define your environment as a series of layers, and configure each layer as a tier of your application.

## Architecture
In OpsWorks, you will be provisioning a stack and layers. The stack is the top-level AWS OpsWorks Stacks entity. It represents a set of instances that you want to manage collectively, typically because they have a common purpose such as serving PHP applications. In addition to serving as a container, a stack handles tasks that apply to the group of instances as a whole, such as managing applications and cookbooks.

Every stack contains one or more layers, each of which represents a stack component, such as a load balancer or a set of application servers and controls the details of setting up and configuring the instance, installing packages, deploying applications, ...

Each layer description includes a list of the built-in recipes that AWS OpsWorks Stacks runs for each of the layer's lifecycle events.
Each layer in a stack must have at least one instance and can optionally have multiple instances.

Each instance in a stack must be a member of at least one layer, except for registered instances (existing instance imported to the stack). You cannot configure an instance directly, except for some basic settings such as the SSH key and hostname. You must create and configure an appropriate layer, and add the instance to the layer.

OpsWorks can be integrated with on-premises servers

A canary deployment is not supported by AWS OpsWorks, only blue/green.

## Auto Healing
Every instance has an AWS OpsWorks Stacks agent that communicates regularly with the service. AWS OpsWorks Stacks uses that communication to monitor instance health. If an agent does not communicate with the service for more than approximately five minutes, AWS OpsWorks Stacks considers the instance to have failed.

By default, AWS OpsWorks Stacks automatically installs the latest updates during setup, after an instance finishes booting. AWS OpsWorks Stacks does not automatically install updates after an instance is online, to avoid interruptions such as restarting application servers. Instead, you manage updates to your online instances yourself, so you can minimize any disruptions.

## Patching
Create and start new instances to replace your current online instances. Then delete the current instances. The new instances will have the latest set of security patches installed during setup.

On Linux-based instances in Chef 11.10 or older stacks, run the Update Dependencies stack command, which installs the current set of security patches and other updates on the specified instances.

AWS updates Windows AMIs for each set of patches, so when you create an instance, it will have the latest updates. However, AWS OpsWorks Stacks does not provide a way to apply updates to online Windows instances. The simplest way to ensure that Windows is up to date is to replace your instances regularly, so that they are always running the latest AMI.

# BeanStalk
The blue environment represents the current application while the green represents the new one.

## BeanStalk DeploymentPolicy
* AllAtOnce – Disables rolling deployments and always deploys to all instances simultaneously.
* Rolling – Enables standard rolling deployments.
* RollingWithAdditionalBatch – Launches an extra batch of instances, before starting the deployment, to maintain full capacity.
* Immutable – Launch a full set of new instances running the new version of the application in a separate Auto Scaling group, alongside the instances running the old version.

-> None of these deployment method do inplace upgrade (even the zero downtime ones).

# CI/CD on AWS
## AWS CodeCommit
A fully-managed source control service that hosts secure Git-based repositories. CodeCommit makes it easy for teams to collaborate on code in a secure and highly scalable ecosystem. This solution uses CodeCommit to create a repository to store the application and deployment codes.

## AWS CodeBuild
A fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy, on a dynamically created build server. This solution uses CodeBuild to build and test the code, which we deploy later.

Typically, AWS CodeBuild cannot access resources in a VPC. To enable access, you must provide additional VPC-specific configuration information in your CodeBuild project configuration. This includes the VPC ID, the VPC subnet IDs, and the VPC security group IDs. VPC-enabled builds can then access resources inside your VPC.

## AWS CodeDeploy
A fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers.

* Deploy code or applications onto a set of EC2 instances running CodeDeploy agents.
* Store multiple application versions and has customizable logic to control deployment behavior.
* Can remove an instance from the ELB during deployment, suspending health checks, and reinstate it at the end.
* Good only for deployment, no set up, no un-deploy or shutdown.
* A deployment configuration is a set of rules and success and failure conditions used by CodeDeploy during a deployment. These rules and conditions are different, depending on whether you deploy to an EC2/On-Premises compute platform, AWS Lambda compute platform, or Amazon ECS compute platform. [Source](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html)
  * CodeDeployDefault.AllAtOnce / CodeDeployDefault.HalfAtATime / CodeDeployDefault.OneAtATime
  * CodeDeployDefault.ECSLinear10PercentEvery1Minutes / CodeDeployDefault.ECSCanary10Percent5Minutes / CodeDeployDefault.ECSAllAtOnce
  * CodeDeployDefault.LambdaCanary10Percent5Minutes / CodeDeployDefault.LambdaLinear10PercentEvery3Minutes / CodeDeployDefault.LambdaAllAtOnce 

## AWS CodePipeline
A fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. This solution uses CodePipeline to create an end-to-end pipeline that fetches the application code from CodeCommit, builds and tests using CodeBuild, and finally deploys using CodeDeploy.

## Deployment Configurations on an AWS Lambda Compute Platform
When you deploy to an AWS Lambda compute platform, the deployment configuration specifies the way traffic is shifted to the new Lambda function versions in your application.

There are three ways traffic can shift during a deployment:
* Canary: Traffic is shifted in two increments. You can specify the percentage of traffic shifted to your updated Lambda function version in the first increment and the interval, in minutes, before the remaining traffic is shifted in the second increment.
* Linear: Traffic is shifted in equal increments with an equal number of minutes between each increment (configurable)
* All-at-once: All traffic is shifted from the original Lambda function to the updated Lambda function

# AWS IoT
## AWS IoT Device Management
Helps you register, organize, monitor, and remotely manage IoT devices at scale. Integrate with AWS IoT Core to easily connect and manage devices in the cloud and with AWS IoT Device Defender to audit and monitor your fleet’s security posture. Swiftly onboard and organize your devices into flexible hierarchies to streamline fleet maintenance and workflow updates. Securely and remotely monitor your device fleet health status, analyze trends, troubleshoot, and push updates at scale.

## AWS IoT Core
Fully Managed MQTT Message Broker that lets you connect billions of IoT devices and route trillions of messages to AWS services without managing infrastructure.
 Connect, manage, and scale your device fleets easily and reliably without provisioning or managing servers.
 Choose your preferred communication protocol, including MQTT, HTTPS, MQTT over WSS, and LoRaWAN.
 Secure device connections and data with mutual authentication and end-to-end encryption.

## AWS IoT Shadows
Allow to query a device without a direct connection (Read and write).
Devices talk to a gateway, their states are saved in shadows, they also can receive config updates this way.
* Topic: Subscribe with MQTT protocol to a thing event.
* IoT Rule: Trigger something (Lambda, cloudwatch, step functions, dynamoDB) according to a metric or a thing action.
* Basic Ingest: directly from the thing to the rule, no cost for pub/sub messaging.

## AWS IoT Greengrass
Seamlessly extends AWS to edge devices so they can act locally on the data they generate, while still using the cloud for management, analytics, and durable storage. With AWS IoT Greengrass, connected devices can run AWS Lambda functions, Docker containers, or both, execute predictions based on machine learning models, keep device data in sync, and communicate with other devices securely – even when not connected to the Internet.

## AWS IoT Events
AWS IoT Events enables you to monitor your equipment or device fleets for failures or changes in operation, and to trigger actions when such events occur. AWS IoT Events continuously watches IoT sensor data from devices, processes, applications, and other AWS services to identify significant events so you can take action. You can use AWS IoT Events to build complex event monitoring applications in the AWS Cloud that you can access through the AWS IoT Events console or APIs.

## AWS IoT Analytics
Simplifies the difficult steps required to analyze massive volumes of IoT data, without the cost and complexity of building an IoT analytics platform. Filter, transform, clean, enrich, and store device data in time-series format for fast retrieval and analysis. Perform analytics and machine learning (ML) inference with hosted Jupyter notebooks to build and train models without managing infrastructure. Analyze device performance and visualize trends with a built-in SQL query engine and integration with Amazon QuickSight.

# Divers
## Amazon Rekognition
You just provide an image or video to the Rekognition API, and the service can identify the objects, people, text, scenes, and activities, as well as detect any inappropriate content.

## AWS Firewall Manager
AWS Firewall Manager is a security management service that makes it easier to centrally configure and manage AWS WAF rules across your accounts and applications. Using Firewall Manager, you can easily roll out AWS WAF rules for your Application Load Balancers and Amazon CloudFront distributions across accounts in AWS Organizations.

## Amazon Guard Duty
A threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts and workloads. GuardDuty analyzes continuous streams of meta-data generated from your account and network activity found in AWS CloudTrail Events, Amazon VPC Flow Logs, and DNS Logs. It also uses integrated threat intelligence such as known malicious IP addresses, anomaly detection, and machine learning to identify threats more accurately.

## Amazon Inspector
A vulnerability management service that continuously scans your AWS workloads for software vulnerabilities and unintended network exposure. Amazon Inspector automatically discovers and scans running Amazon EC2 instances, container images in Amazon Elastic Container Registry (Amazon ECR), and AWS Lambda functions for known software vulnerabilities and unintended network exposure

Though not required in all cases, you should install the Amazon Inspector agent on each of your target Amazon EC2 instances in order to fully assess their security.

Amazon Inspector creates a finding when it discovers a software vulnerability or network configuration issue. A finding describes the vulnerability, identifies the affected resource, rates the severity of the vulnerability, and provides remediation guidance. You can analyze findings using the Amazon Inspector console, or view and process your findings through other AWS services. For more information, see Understanding findings in Amazon Inspector.

## AWS Trusted Advisor
An online tool that provides you real time guidance to help you provision your resources following AWS best practices. Reduce Costs, Increase Performance, and Improve Security. Number of checks depends of the AWS support level.

Trusted Advisor will notify you once you reach more than 80% of a service quota. You can then follow recommendations to delete resources or request a quota increase.

## Amazon Fraud Detector
A fully managed service enabling customers to identify potentially fraudulent activities and catch more online fraud faster.
Build, deploy, and manage fraud detection models without previous machine learning (ML) experience.
Gain insights from your historical data, plus 20+ years of Amazon experience, to construct an accurate, customized fraud detection model.

## AWS Network Firewall
Define firewall rules that provide fine-grained control over network traffic. Network Firewall works together with AWS Firewall Manager so you can build policies based on Network Firewall rules and then centrally apply those policies across your virtual private clouds (VPCs) and accounts. Protect your unique workloads with a flexible firewall engine that can define thousands of custom rules. Centrally manage security policies across existing accounts and VPCs, and automatically enforce mandatory policies on new accounts.

## AWS Compute Optimizer
Helps avoid overprovisioning and underprovisioning four types of AWS resources: EC2 instance types, EBS volumes, ECS services on AWS Fargate, and AWS Lambda functions, based on your utilization data. To receive Amazon EC2 or Auto Scaling group recommendations that capture monthly or quarterly utilization patterns, you can activate **enhanced** infrastructure metrics, a paid feature.

## Amazon BYOIP
Bring part or all of your public IPv4 address range from your on-premises deployment to your AWS account.

## Amazon Simple Email Service (Amazon SES)
A cloud-based email sending service designed to help digital marketers and application developers send marketing, notification, and transactional emails. It is a reliable, cost-effective service for businesses of all sizes that use email to keep in contact with their customers.

## Amazon Data Lifecycle Manager (DLM)
Provides a simple, automated way to back up data stored on Amazon EBS volumes using snapshots. You can define backup and retention schedules for EBS snapshots by creating lifecycle policies based on tags. With this feature, you no longer have to rely on custom scripts to create and manage your backups.

## Mobile Push for Amazon SNS
You have the ability to send push notification messages directly to apps on mobile devices. Push notification messages sent to a mobile endpoint can appear in the mobile app as message alerts, badge updates, or even sound alerts.

## Amazon Pinpoint
Is AWS’s Digital User Engagement Service that enables AWS customers to effectively communicate with their end users and measure user engagement across multiple channels including email, Text Messaging (SMS) and Mobile Push Notifications.

Amazon Pinpoint also provides tools that enables audience management and segmentation, campaign management, scheduling, template management, A/B testing, analytics and data integration. It captures data to track deliverability as well as usage and messaging analytics.

## AWS AppSync
Simplifies application development by letting you create a flexible API to securely access, manipulate, and combine data from one or more data sources. AppSync is a managed service that uses GraphQL to make it easy for applications to get exactly the data they need.

Build application requiring real-time updates on a range of data sources such as NoSQL data stores, relational databases, HTTP APIs, and your custom data sources with AWS Lambda. For mobile and web apps, AppSync additionally provides local data access when devices go offline, and data synchronization with customizable conflict resolution, when they are back online.

## AWS Device Farm
An application testing service that lets you improve the quality of your web and mobile apps by testing them across an extensive range of desktop browsers and real mobile devices

## Amazon AppStream 2.0
A fully managed application streaming service. You centrally manage your desktop applications on AppStream 2.0 and securely deliver them to any computer. Each user has a fluid and responsive experience with your applications, including GPU-intensive 3D design.
* CITRIX competitor

## Amazon WorkSpaces
A managed, secure Desktop-as-a-Service (DaaS) solution. You can use Amazon WorkSpaces to provision either Windows or Linux desktops

The user experience in AppStream 2.0 is more streamlined than in Workspaces. In Workspaces you have an RDP Connection to the virtual machine so you get access to the Windows functionality. In AppStream 2.0 the user can see only the available applications for use. No additional Windows controls are available.

## AWS VPN CloudHub
Operates on a simple hub-and-spoke model that you can use with or without a VPC. Use this design if you have multiple branch offices and existing internet connections and would like to implement a convenient, potentially low cost hub-and-spoke model for primary or backup connectivity between these remote offices.
* Correspond à un réseau en étoile !

## AWS X-Ray
Helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

## AWS Service Catalog
Allows organizations to create and manage catalogs of IT services that are approved for use on AWS. These IT services can include everything from virtual machine images, servers, software, and databases to complete multi-tier application architectures.

* Without a launch constraint, end users must launch and manage products using their own IAM credentials. To do so, they must have permissions for AWS CloudFormation, the AWS services used by the products, and AWS Service Catalog. By using a launch role, you can instead limit the end users' permissions to the minimum that they require for that product.

## Amazon MQ
A managed message broker service for Apache ActiveMQ that makes it easy to set up and operate message brokers in the cloud. Connecting your current applications to Amazon MQ is easy because it uses industry-standard APIs and protocols for messaging, including JMS, NMS, AMQP, STOMP, MQTT, and WebSocket. Using standards means that in most cases, there’s no need to rewrite any messaging code when you migrate to AWS.

## AWS SQS
Maximum message size is 262,144 bytes (256 KB)

## AWS-Generated Cost Allocation Tags
The AWS generated tags createdBy is a tag that AWS defines and applies to supported AWS resources for cost allocation purposes. To use the AWS generated tags, a master account owner must activate it in the Billing and Cost Management console. When a master account owner activates the tag, the tag is also activated for all member accounts.

## Amazon MSK
A fully managed service that makes it easy for you to build and run applications that use Apache Kafka to process streaming data. Apache Kafka is an open-source platform for building real-time streaming data pipelines and applications. With Amazon MSK, you can use native Apache Kafka APIs to populate data lakes, stream changes to and from databases, and power machine learning and analytics applications.

## Amazon WorkDocs
A fully managed, secure content creation, storage, and collaboration service. With Amazon WorkDocs, you can easily create, edit, and share content, and because it’s stored centrally on AWS, access it from anywhere on any device.
* Google Docs and Office 365 competitor

## Amazon Simple Workflow Service (SWF)
Helps developers build, run, and scale background jobs that have parallel or sequential steps. You can think of Amazon SWF as a fully-managed state tracker and task coordinator in the Cloud.

* Not really used anymore, require too much custom coding.
* Do not work well with private subnets
* Step Functions are now the recommended service from AWS for most workflow style processes.

## EC2Rescue
For Linux and Windows. An easy-to-use, open-source tool that can be run on an Amazon EC2 instance to diagnose and troubleshoot common issues using its library of over 100 modules. A few generalized use cases for EC2Rescue for Linux include gathering syslog and package manager logs, collecting resource utilization data, and diagnosing/remediating known problematic kernel parameters and common OpenSSH issues.

## Amazon Mechanical Turk
The notifications are sent whenever certain events occur during the life cycle of Human Intelligence Task (HIT) only to SQS or SNS, but not to Amazon SWF.

## Amazon Quantum Ledger Database (QLDB)
A fully managed ledger database that provides a transparent, immutable, and cryptographically verifiable transaction log ‎owned by a central trusted authority. Amazon QLDB tracks each and every application data change and maintains a complete and verifiable history of changes over time.

## AWS Mobile Hub
A collection of Amazon Web Services tools designed to help developers build, test, configure and release cloud-based applications for mobile devices. AWS Mobile Hub packages its tools as a console for developers, allowing them to quickly select desired features for applications and integrate them into code.
It includes a variety of tools, to track application analytics, manage end-user access and storage, set up push notifications, deliver content, and build back-end services.
It uses a variety of native services, including Amazon Mobile Analytics, Amazon S3, Amazon DynamoDB and Amazon SNS, to execute and manage these features.

## EC2 Image Builder
EC2 Image Builder simplifies the creation, maintenance, validation, sharing, and deployment of Linux or Windows Server images for use with Amazon EC2 and on-premises.

Image Builder significantly reduces the effort of keeping images up-to-date and secure by providing a simple graphical interface, built-in automation, and AWS-provided security settings. With Image Builder, there are no manual steps for updating an image nor do you have to build your own automation pipeline.

Image Builder is offered at no additional cost.

## Amazon Lightsail
An easy-to-use cloud platform that offers you everything needed to build an application or website, plus a cost-effective, monthly plan. Easily deploy a web application with a few clicks with pre-configured development stacks like LAMP, Nginx, MEAN, and Node.js. Quickly create a website that shines, customize your blog, e-commerce, or personal website with Lightsail's pre-configured applications like WordPress, Magento, Plesk, and Joomla.

## Amazon FSx
Launch and run popular file systems. With Amazon FSx, you can leverage the rich feature sets and fast performance of widely-used open source and commercially-licensed file systems, while avoiding infrastructure management. It provides cost-efficient capacity and high levels of reliability, and it integrates with other AWS services so that you can manage and use the file systems in cloud-native ways.

Amazon FSx provides you with two file systems to choose from: Amazon FSx for Windows File Server for business applications and Amazon FSx for Lustre for high-performance workloads.
* No automatic volume size increase
* The FSx for Lustre filesystem can temporarily load the data from S3 and share it among the application tier instances. This is cheaper as we can use S3 for long-term storage and FSx for Lustre for the temporary shared file system. When linked to an Amazon S3 bucket, an FSx for Lustre file system transparently presents S3 objects as files.

## Amazon Elastic Transcoder
Media transcoding in the cloud. It is designed to be a highly scalable, easy to use and a cost effective way for developers and businesses to convert (or “transcode”) media files from their source format into versions that will playback on devices like smartphones, tablets and PCs.

## Amazon Timestream
A fast, scalable, fully managed time series database service for IoT and operational applications that makes it easy to store and analyze trillions of events per day at 1/10th the cost of relational databases.

Timestream is ideal for the time-based GPS data, data typically arriving in time order form, data that is append-only, and queries that are always over a time interval.

## Amazon Neptune
Makes it easy to build and run applications that work with highly connected datasets using a high-performance graph database engine optimized for storing billions of relationships and querying the graph with milliseconds latency. Amazon Neptune supports popular graph models Property Graph and W3C's RDF, and their respective query languages Apache TinkerPop Gremlin and SPARQL, allowing you to easily build queries that efficiently navigate highly connected datasets.

The default for any social media-style database should be Neptune, which is a managed graph database that can support dynamic relationships between data which are as important as the data itself.

## AWS Batch
Enables developers, scientists, and engineers to easily and efficiently run hundreds of thousands of batch computing jobs on AWS. AWS Batch dynamically provisions the optimal quantity and type of compute resources (e.g., CPU or memory optimized instances) based on the volume and specific resource requirements of the batch jobs submitted. With AWS Batch, there is no need to install and manage batch computing software or server clusters that you use to run your jobs, allowing you to focus on analyzing results and solving problems. AWS Batch plans, schedules, and executes your batch computing workloads across the full range of AWS compute services and features, such as Amazon EC2 and Spot Instances.

There is no additional charge for AWS Batch. You only pay for the AWS resources (e.g. EC2 instances) you create to store and run your batch jobs.

### AWS Batch Job queues
Jobs are submitted to a job queue where they reside until they can be scheduled to run in a compute environment. An AWS account can have multiple job queues. For example, you can create a queue that uses Amazon EC2 On-Demand instances for high priority jobs and another queue that uses Amazon EC2 Spot Instances for low-priority jobs. Job queues have a priority that's used by the scheduler to determine which jobs in which queue should be evaluated for execution first.

## Amazon Connect
An easy to use omnichannel cloud contact center that helps companies provide superior customer service at a lower cost and can scale to support millions of customers.
Designed from the ground up to be omnichannel, Amazon Connect provides a seamless experience across voice and chat for your customers and agents. This includes one set of tools for skills-based routing, powerful real-time and historical analytics, and easy-to-use intuitive management tools.

## AWS Budgets
Gives you the ability to set custom budgets that alert you when your costs or usage exceed (or are forecasted to exceed) your budgeted amount. You can also use AWS Budgets to set reservation utilization or coverage targets and receive alerts when your utilization drops below the threshold you define. Reservation alerts are supported for Amazon EC2, Amazon RDS, Amazon Redshift, Amazon ElastiCache, and Amazon Elasticsearch reservations.

## Amazon Lex
A service for building conversational interfaces (chat bot) into any application using voice and text. Amazon Lex provides the advanced deep learning functionalities of automatic speech recognition (ASR) for converting speech to text, and natural language understanding (NLU) to recognize the intent of the text, to enable you to build applications with highly engaging user experiences and lifelike conversational interactions. (Technologies that power Amazon Alexa are now available to any developer)

## Amazon Comprehend
A natural-language processing (NLP) service that uses machine learning to uncover valuable insights and connections in text. Amazon Comprehend is used to derive and understand valuable insights from text within documents. It doesn't have automatic speech recognition (ASR) for converting speech to text nor natural language understanding (NLU). You have to use Amazon Lex for this scenario.

## Amazon Kendra
Use with Large Language Models (LLMs) to quickly create secure, generative AI-powered conversational experiences for your users on top of your enterprise content. Find answers faster with intelligent enterprise search powered by machine learning. Quickly implement a unified search experience across multiple structured and unstructured content repositories. Use natural language processing (NLP) to get highly accurate answers without the need for machine learning (ML) expertise.

## Amazon Translate
A neural machine translation service that delivers fast, high-quality, affordable, and customizable language translation. Build batch and real-time translation capabilities into your applications with a single API call. Customize your machine learning (ML)–translated output to define brand names, model names, and other unique terms. Localize content for diverse global users and translate and analyze large volumes of text to activate cross-lingual communication between users.

## Amazon Transcribe
Automatically convert speech to text. Get insights from customer conversations. Create subtitles and meeting notes. Detect toxic content in audio.
Medical doctors and practitioners can use Amazon Transcribe Medical to quickly and efficiently document clinical conversations into electronic health record (EHR) systems for analysis.

## Amazon CloudSearch
A managed service in the AWS Cloud that makes it simple and cost-effective to set up, manage, and scale a search solution for your website or application. It supports 34 languages and popular search features such as highlighting, autocomplete, and geospatial search. Hardware and software provisioning, setup and configuration, software patching, data partitioning, node monitoring, scaling, and data durability are handled for you.

## Amazon Textract
Has a proven track record with scanned documents or similar imagery , which is natural because that is the environment it is designed for. RekognitionDetectImageText and Textract performed comparably well on most samples with Textract doing slightly better with imagery similar to scanned documents.

## AWS Global Accelerator
A networking service that helps you improve the availability, performance, and security of your public applications. Global Accelerator provides two global static public IPs that act as a fixed entry point to your application endpoints, such as Application Load Balancers, Network Load Balancers, Amazon Elastic Compute Cloud (EC2) instances, and elastic IPs.
AWS Global Accelerator provides improved performance and high availability when you have copies of your application running in multiple AWS Regions.

## Gateway Load Balancer (GWLB)
Helps you easily deploy, scale, and manage your third-party virtual appliances. It gives you one gateway for distributing traffic across multiple virtual appliances while scaling them up or down, based on demand. This decreases potential points of failure in your network and increases availability.

You can find, test, and buy virtual appliances from third-party vendors directly in AWS Marketplace.

## AWS App Runner
A fully managed container application service that lets you build, deploy, and run containerized web applications and API services without prior infrastructure or container experience. If you want to turn your code into an App Runner service without having to build or push the container, you can use a GitHub repo directly.

## AWS Proton
A deployment workflow tool for modern applications that helps platform and DevOps engineers achieve organizational agility. Amplify platform engineering impact by implementing scalable self-service capabilities for developers. Empower developers to move faster with a self-service tool to provision infrastructure and manage code deployment. AWS Proton support for defining infrastructure in HashiCorp Configuration Language (HCL) and provisioning infrastructure using Terraform.

## Amazon AppFlow
Automate data flows between software as a service (SaaS) and AWS services. Automate bi-directional data flows between SaaS applications and AWS services in just a few clicks. Run the data flows at the frequency you choose, whether on a schedule, in response to a business event, or on demand. Simplify data preparation with transformations, partitioning, and aggregation. Automate preparation and registration of your schema with the AWS Glue Data Catalog so you can discover and share data with AWS analytics and machine learning services. Fully managed integration service to transfer data between services such as Salesforce, SAP, Google Analytics, and Amazon Redshift.

## AWS Amplify
A complete solution that lets frontend web and mobile developers easily build, ship, and host full-stack applications on AWS, with the flexibility to leverage the breadth of AWS services as use cases evolve. No cloud expertise needed.

## AWS Ground Station
A fully managed service that lets you control satellite communications, process data, and scale your operations without having to worry about building or managing your own ground station infrastructure. Satellites are used for a wide variety of use cases, including weather forecasting, surface imaging, communications, and video broadcasts. Ground stations form the core of global satellite networks.

## AWS Elemental MediaConnect
A high-quality transport service for live video. It delivers the reliability and security of satellite and fiber-optic combined with the flexibility, agility, and economics of IP-based networks.

## AWS Lake Formation
Easily creates secure data lakes, making data available for wide-ranging analytics. Simplify security management and governance at scale, and enable fine-grained permissions across your data lake. Break down data silos and make all data discoverable with a centralized data catalog. Support organization-wide data accessibility at scale with cross-account data sharing.

## AWS Backup
A fully-managed service that makes it easy to centralize and automate data protection across AWS services, in the cloud and on-premises. Using this service, you can configure backup policies and monitor activity for your AWS resources in one place. It allows you to automate and consolidate backup tasks that were previously performed service-by-service and remove the need to create custom scripts and manual processes. With a few clicks in the AWS Backup console, you can automate your data protection policies and schedules.

## Tag Editor
Add tags to multiple, supported resources at once. You build a query for resources of various types, and then add, remove, or replace tags for the resources in your search results. Tag-based queries assign an AND operator to tags, so any resource that matches the specified resource types and all specified tags is returned by the query.

The AWS-generated cost allocation tags **createdBy** is a tag that AWS defines and applies to supported AWS resources for cost allocation purposes. To use the AWS-generated tag, a management account owner must activate it in the Billing console.

## AWS Cost and Usage Reports (AWS CUR)
Contains the most comprehensive set of cost and usage data available. You can publish your AWS billing reports to an Amazon Simple Storage Service (Amazon S3) bucket that you own. You can receive reports that break down your costs by the hour, day, or month, by product or product resource, or by tags that you define yourself.

AWS updates the report in your bucket once a day in comma-separated value (CSV) format. You can view the reports using spreadsheet software such as Microsoft Excel or Apache OpenOffice Calc, or access them from an application using the Amazon S3 API.
You can use the Cost & Usage Reports page in the Billing and Cost Management console to create these Reports.

In AWS Organizations, both management accounts and member accounts can create Cost and Usage Reports.

## Application Auto Scaling
A web service for developers and system administrators who need a solution for automatically scaling their scalable resources for individual AWS services beyond Amazon EC2. With Application Auto Scaling, you can configure automatic scaling for the following resources:
*	Aurora replicas
*	DynamoDB tables and global secondary indexes
* Amazon Elastic Container Service (ECS) services
*	ElastiCache for Redis clusters (replication groups)
*	Amazon EMR clusters
*	Amazon Keyspaces (for Apache Cassandra) tables
*	Lambda function provisioned concurrency
*	Spot Fleet requests
