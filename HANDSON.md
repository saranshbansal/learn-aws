# AWS CLI setup
First login to IAM console and create a new user. 
1. Click user.
2. Create new “security credential”. 
3. Also, grant relevant permissions like ec2, s3 full access etc to the user. 

Once done, configure this user in `AWS CLI`
1. Run `aws configure`
2. Add above `access key` and `secret` and rest can be blank.
3. Run any aws command like `aws  s3 ls`

## Common aws cli commands:
```
`aws help`

`aws help ec2`

`aws help s3 ls` # shows list of buckets

`aws s3 cp testfile_via_cli.txt s3://sg02-training-bucket` # copy file to given bucket

`aws s3 ls s3://sg02-training-bucket` # show all files in given bucket
```

### Other useful commands:
```
`cat .aws/config` # check configurations

`Cat .aws/credentials` # check cress
```

### EC2 command list
```
       o restore-snapshot-from-recycle-bin

       o restore-snapshot-tier

       o revoke-client-vpn-ingress

       o revoke-security-group-egress

       o revoke-security-group-ingress

       o run-instances

       o run-scheduled-instances

       o search-local-gateway-routes

       o search-transit-gateway-multicast-groups

       o search-transit-gateway-routes

       o send-diagnostic-interrupt

       o start-instances

       o start-network-insights-access-scope-analysis

       o start-network-insights-analysis

       o start-vpc-endpoint-service-private-dns-verification

       o stop-instances

       o terminate-client-vpn-connections

       o terminate-instances

       o unassign-ipv6-addresses

       o unassign-private-ip-addresses

       o unassign-private-nat-gateway-address

       o unmonitor-instances

       o update-security-group-rule-descriptions-egress

       o update-security-group-rule-descriptions-ingress

       o wait

       o withdraw-byoip-cidr
```

### S3 command list

#### Basic commands:
```
       o cp
       o ls
       o mb
       o mv
       o presign
       o rb
       o rm
       o sync
       o website
```
#### Miscellaneous commands:

This command will exclude /tmp/foo/bar.txt and /tmp/foo/baz.jpg:
`aws s3 cp /tmp/foo/ s3://bucket/ --recursive --exclude "ba*"`

Example:
```
/tmp/foo/ba* -> /tmp/foo/.git/config      (does not match, should include)
/tmp/foo/ba* -> /tmp/foo/.git/description (does not match, should include)
/tmp/foo/ba* -> /tmp/foo/foo.txt          (does not match, should include)
/tmp/foo/ba* -> /tmp/foo/bar.txt  (matches, should exclude)
/tmp/foo/ba* -> /tmp/foo/baz.jpg  (matches, should exclude)
```

This command will upload only files ending with .jpg:

`aws s3 cp /tmp/foo/ s3://bucket/ --recursive --exclude "*" --include "*.jpg"`

If you wanted to include both .jpg files as well as .txt files you can run:

`aws s3 cp /tmp/foo/ s3://bucket/ --recursive --exclude "*" --include "*.jpg" --include "*.txt"`


# VPC setup

1.	Login to IAM account
2.	Got to VPC service
3.	Create new VPC by providing a custom range: 10.0.0.0/16
4.	Edit VPC settings and select “Enable DNS hostnames”
5.	Create public and private subnets by going to Subnet and assigning IP CIDR blocks (ex. 10.0.1.0/24 ,10.0.2.0/24…). Chose AZ as US-east-1a
6.	Create a routing tables – private subnets and public subnets called MAIN-RT. 
7.	Go to routing tables individually and associate subnets to them.
8.	Got to internet gateway > actions > attach your vpc
9.	Got back to route table > chose main route table and go to “routes”.
10.	Create new route and associate igw with destination as 0.0.0.0/0.
11.	Create a NAT gateway and associate it with a PUBLIC subnet. Allocate an Elastic IP and create it.
12.	Go to route table, select private RT, click add route, and associate a NAT gateway this time like in step 10.
13.	Create a Security Group Public-Web and add inbound and outbound rules. Keep it all traffic open for now.


# Steps to launch an EC2 instance
 
### Command:
`aws ec2 run-instances –image-id <value> --instance-type <value> --security-group-ids <value> --subnet-id <value> --key-name <value> --user-data file:// <value>`

Setup your VPC with all the components. You need to create ec2 instances for all the public/private subnets as below:
 
1.	Go to EC2 dashboard and launch new instance. Grab Amazon AMI Id:
 
2.	Instance type = t2.micro
 
3.	Go back to VPC dashboard -> Security groups. Chose Public-Web security group and copy the SG id:
 
4.	Go to Subnets and copy Public 1A subnet id:
 
5.	Key name is key-pair you use in EC2. Go back to EC2 console -> Key pairs and just copy name:
 
6.	User data is a file with bunch of script to show some details in a web page. Provide the path to that file:
 
7.	Go to AWS CLI and run the command. Your new instance will launch. Rename it to Public 1A. Do the same for other public and private subnets


# Steps to launch an Auto-scaling Web App on EC2

- Open EC2 page
- Create a new launch template with following changes:
- Name it MyEC2WebApp
- Select AMI (Amazon linux)
- Chose instance type (t2.micro)
- Chose your keypair (create new if not already)
- Under network setting, attached a subnet and security group (or create new)
- Under “Advance network” configs, enable “Auto-assign public IP”.
- Under “Advance details”, scroll at the very bottom to provide some user data which will be a web app using some external packages.

```
#!/bin/bash
yum update -y 
yum install -y httpd 
systemctl start httpd 
systemctl enable- httpd
EC2AZ-$(curt -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
echo "<center><hl>This Amazon EC2 instance is located in Availability Zone: AZID </h1></center> > /var/www/html/index.txt
sed "s/AZID/$EC2AZ/" /var/ww/html/index.txt > /var/www/html/index.html
```

-	Launch instance.
-	Click View template, open Auto-scaling group section. Create a new ASG group and name it ASG1.
-	Choose your newly created launch template and click next.
-	Choose Network settings: VPC and subnets. Only select all public subnets.
-	Keep clicking next till you see summary. No need to change anything else apart from those.  
-	Make sure your Security Group does have http rule enabled. Select your ASG and check.
-	If HTTP type rule is not there, edit SG and add it.
-	Go back to “Instances” section and you should have a running instance which is your web app. Select and check ip address to access it.