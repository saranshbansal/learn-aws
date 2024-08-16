# What is DNS?

- Domain Name System which translates the human friendly hostname into the machine IP addresses
- www.google.com => 172.217.18.36
- DNS is the backbone of the Internet
- DNS uses hierarchical naming structure
  - .com
  - example.com
  - www.example.com
  - api.example.com

## DNS Terminologies

- Domain Registrar: Amazon Route 53, GoDaddy, ...
- DNS Records: A, AAAA, CNAME, NS, ...
- Zone File: contains DNS records
- Name Server: resolves DNS queries (Authoritative or Non-Authoritative) • Top Level Domain (TLD): .com, .us, .in, .gov, .org, ...
- Second Level Domain (SLD): amazon.com, google.com,...

## How DNS Works

- DNS is a system used to map domain names (such as www.example.com) to IP addresses (such as 9.10.11.12).
- When a user enters a domain name into their web browser, the browser sends a request to a DNS server to resolve the domain name to an IP address.
- The DNS server first checks its cache to see if it has recently resolved the domain name to an IP address.
- If the DNS server has the resolution in its cache, it returns that IP address to the browser.
- If the DNS server does not have the resolution in its cache, it sends a request to other DNS servers to resolve the domain name.
- This process continues until a DNS server that has the resolution is found.
- The IP address is returned to the browser, which then uses it to connect to the web server associated with that IP address.

- DNS is hierarchical in nature, with several types of DNS servers that work together to resolve domain names to IP addresses.
  - **Root DNS Server**: At the top of the hierarchy is the Root DNS server. It contains information about the top-level domain (TLD) servers, such as .com, .org, and .edu. When a DNS request is made, the root server is the first point of contact and it responds with the IP address of the appropriate TLD server.
  - **TLD DNS Server**: TLD servers are responsible for managing the information for the specific TLDs they are responsible for. For example, a .com TLD server will have information about all the domain names that end in .com. When a request is made to a TLD server, it responds with the IP address of the appropriate second-level domain (SLD) server.
  - **SLD DNS Server**: SLD servers are the final step in the DNS resolution process. They contain information about specific domain names and their associated IP addresses. When a request is made to an SLD server, it responds with the IP address associated with the domain name.
- Each step of the DNS resolution process is performed in order, starting with the root server and ending with the SLD server. This allows for efficient and accurate resolution of domain names to IP addresses, even as new domain names are added or existing ones change IP addresses.

# Amazon Route 53
Amazon Route 53 is a highly available and scalable `Domain Name System (DNS)` web service designed to provide highly reliable and cost-effective routing of end users to internet applications.

Amazon Route 53 offers the following functions:

- Domain name registry.
- DNS resolution.
- Health checking of resources.

Route 53 can perform any combination of these functions.

You can control management access to your Amazon Route 53 hosted zone by using IAM.

There is a default limit of 50 domain names, but this can be increased by contacting support.

Private DNS is a Route 53 feature that lets you have authoritative DNS within your VPCs without exposing your DNS records including the name of the resource and its IP address(es) to the Internet.

## Hosted Zones
A hosted zone is a collection of records for a specified domain.

There are two types of zones:

**Public host zone** – determines how traffic is routed on the Internet.

**Private hosted zone for VPC** – determines how traffic is routed within `VPC` (resources are not accessible outside the VPC). 

You can associate the Route 53 private hosted zone in one account with a `VPC` in another account.

## Alias records
The Alias record is a Route 53 specific record type.

You can use Alias records to map custom domain names (such as api.example.com) both to API Gateway custom regional APIs and edge-optimized APIs and to Amazon VPC interface endpoints.

An alias record can only point to a `CloudFront distribution, Elastic Beanstalk environment, ELB, S3 bucket as a static website`, or to `another record in the same hosted zone` that you’re creating the alias record in.

- Maps a hostname to an AWS resource
- An extension to DNS functionality
- Automatically recognizes changes in the resource’s IP addresses
- Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex), e.g.: example.com Amazon Route 53
- Alias Record is always of type A/AAAA for AWS resources (IPv4 / IPv6)
- **You can’t set the TTL**

### CNAME vs Alias

- AWS Resources (Load Balancer, CloudFront...) expose an AWS hostname:
  - lb1-1234.us-east-2.elb.amazonaws.com and you want myapp.mydomain.com
- **CNAME**:
  - Points a hostname to any other hostname. (app.mydomain.com => blabla.anything.com)
  - **ONLY FOR NON ROOT DOMAIN (aka. something.mydomain.com)**
- **Alias**:
  - Points a hostname to an AWS Resource (app.mydomain.com => blabla.amazonaws.com)
  - **Works for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com)**
  - Free of charge
  - Native health check

## Routing Policies
Routing policies determine how Route 53 responds to queries.

The following table highlights the key function of each type of routing policy:

| Policy            | What it Does                                                                        |
| ----------------- | ----------------------------------------------------------------------------------- |
| Simple            | Simple DNS response providing the IP address associated with a name                 |
| Failover          | If primary is down (based on health checks), routes to secondary destination        |
| Geolocation       | Uses geographic location you’re in (e.g. Europe) to route you to the closest region |
| Geoproximity      | Routes you to the closest region within a geographic area                           |
| Latency           | Directs you based on the lowest latency route to resources                          |
| Multivalue answer | Returns several IP addresses and functions as a basic load balancer                 |
| Weighted          | Uses the relative weights assigned to resources to determine which to route to      |

### Simple Routing Policy
An A record is associated with one or more IP addresses.

Uses round robin.

Does not support health checks.

The following diagram depicts an Amazon Route 53 Simple routing policy configuration:

![alt text](images/image-r53_srp.png)

### Failover Routing Policy

- Failover to a secondary IP address.
- Associated with a health check.
- Used for active-passive.
- Routes only when the resource is healthy.
- Can be used with ELB.
- When used with Alias records set Evaluate Target Health to “Yes” and do not use health checks.

### Geo-location Routing Policy
Use for routing traffic based on the location of resources and, optionally, shift traffic from resources in one location to resources in another.

- Caters to different users in different countries and different languages.
- Contains users within a particular geography and offers them a customized version of the workload based on their specific needs.
- Geolocation can be used for localizing content and presenting some or all your website in the language of your users.
- Can also protect distribution rights.
- Can be used for spreading load evenly between regions.
- If you have multiple records for overlapping regions, Route 53 will route to the smallest geographic region.
- You can create a default record for IP addresses that do not map to a geographic location.

The following diagram depicts an Amazon Route 53 Geolocation routing policy configuration:

![alt text](images/image-r53_grp.png)

### Latency Routing Policy
AWS maintains a database of latency from different parts of the world.

- Focused on improving performance by routing to the region with the lowest latency.
- You create latency records for your resources in multiple EC2 locations.

The following diagram depicts an Amazon Route 53 Latency based routing policy configuration:

![alt text](images/image-r53_lrp.png)

### Multi-value Answer Routing Policy
Use for responding to DNS queries with up to eight healthy records selected at random.

### Weighted Routing Policy
- Like simple but you can specify a weight per IP address.
- You create records that have the same name and type and assign each record a relative weight.
- Numerical value that favors one IP over another.
- To stop sending traffic to a resource you can change the weight of the record to 0.

The following diagram depicts an Amazon Route 53 Weighted routing policy configuration:

![alt text](images/image-r53_wrp.png)



## Route 53 - RecordsTTL (TimeTo Live)

- **High TTL – e.g., 24 hr**
  - Less traffic on Route 53
  - Possibly outdated records
- **Low TTL – e.g., 60 sec.**
  - More traffic on Route 53 ($$)
  - Records are outdated for less time
  - Easy to change records
- **Except for Alias records,TTL is mandatory for each DNS record**


### Route 53 - Health Checks

- HTTP Health Checks are only for **public resources**
- Health Check => Automated DNS Failover:
  - Health checks that monitor an endpoint (application, server, other AWS resource)
  - Health checks that monitor other health checks (Calculated Health Checks)
  - Health checks that monitor CloudWatch Alarms (full control !!) – e.g., throttles of DynamoDB, alarms on RDS, custom metrics (helpful for private resources)
- Health Checks are integrated with CW metrics

### Health Checks - Monitor an Endpoint

- **About 15 global health checkers will check the endpoint health**
  - Healthy/UnhealthyThreshold–3(default)
  - Interval – 30 sec (can set to 10 sec – higher cost)
  - Supported protocol:HTTP, HTTPSandTCP
  - If > 18% of health checkers report the endpoint is healthy, Route 53 considers it Healthy. Otherwise, it’s Unhealthy
  - Ability to choose which locations you want Route 53 to use
- Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes
- Health Checks can be setup to pass / fail based on the text in the first **5120 bytes** of the response
- Configure you router/firewall to allow incoming requests from Route 53 Health Checkers

### Route 53 - Calculated Health Checks

- Combine the results of multiple Health Checks into a single Health Check
- You can use **OR, AND, or NOT**
- Can monitor up to 256 Child Health Checks
- Specify how many of the health checks need to pass to make the parent pass
- Usage: perform maintenance to your website without causing all health checks to fail

### Health Checks - Private Hosted Zones

- Route 53 health checkers are outside the VPC
- They can’t access **private** endpoints (private VPC or on-premises resource)
- You can create a **CloudWatch Metric** and associate a **CloudWatch Alarm**, then create a Health Check that checks the alarm itself

![Private Hosted](../images/private_hosted.png)

### Routing Policies - Failover (Active-Passive)

- A failover routing policy is a routing policy used to route traffic to a primary resource, such as an Amazon Elastic Compute Cloud (EC2) instance, while monitoring a secondary resource as a backup.
- If the primary resource becomes unavailable, traffic is automatically routed to the secondary resource.
- This type of routing policy is useful for ensuring high availability of your application by minimizing the impact of a failure of the primary resource.
- AWS Route 53 service supports failover routing policy, where you can configure a primary resource and a secondary resource and Route 53 will monitor the health of both the resources and route traffic to the healthy resource.
- This can be done by creating a health check for each resource, and then configuring the routing policy to route traffic to the primary resource as long as it is healthy, and to the secondary resource if the primary resource becomes unhealthy.

![Failover](../images/failover.png)

### Routing Policies - Geolocation

- Different from Latency-based!
- **This routing is based on user location**
- Specify location by Continent, Country or by US State (if there’s overlapping, most precise location selected)
- Should create a **"Default"** record (in case there’s no match on location)
- Use cases: website localization, restrict content distribution, load balancing, ...
- Can be associated with Health Checks

![Geolocation](../images/geolocation.png)

### Routing Policies - Geoproximity

- Route traffic to your resources based on the geographic location of users and resources
- Ability to **shift more traffic to resources based** on the defined bias
- To change the size of the geographic region, specify bias values:
  - To expand (1 to 99) – more traffic to the resource
  - To shrink (-1 to -99) – less traffic to the resource
- Resources can be:
  - AWS resources (specify AWS region)
  - Non-AWS resources (specify Latitude and Longitude)
- You must use Route 53 **Traffic Flow** to use this feature

## Route 53 - Traffic flow

- Simplify the process of creating and maintaining records in large and complex configurations
- Visual editor to manage complex routing decision trees
- Configurations can be saved as **Traffic Flow Policy**
- Can be applied to different Route 53 Hosted Zones (different domain names)
- Supports versioning
