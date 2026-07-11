# Deploy a 3 Tier Architecture Webapp on AWS 

## Architecture overview
![Architecture overview](./images/diagram.png)
In this architecture, a public-facing Application Load Balancer forwards client traffic to our web tier EC2 instances. The web tier is running Nginx webservers that are configured to serve a React.js website and redirects our API calls to the application tier’s internal facing load balancer. The internal facing load balancer then forwards that traffic to the application tier, which is written in Node.js. The application tier manipulates data in an Aurora MySQL multi-AZ database and returns it to our web tier. Load balancing, health checks and auto scaling groups are created at each layer to maintain the availability of this architecture.

### Set Up 
In this task we will be downloading the code from [Github](https://github.com/aws-samples/aws-three-tier-web-architecture-workshop.git ) and upload it to amazon S3 bucket so our instances can access it. We will also create an AWS Identity and Access Management EC2 role so we can use AWS Systems Manager Session Manager to connect to our instances securely and without needing to create SSH key pairs

- S3 Bucket Created and named mywebapp-ashish-101
- Created IAM EC2 Instance role and named Project-ec2role
- Permission policies AmazonSSMManagedInstanceCore and AmazonS3ReadOnlyAccess added to Project-ec2role

## Networking and Security

### VPC
   - **Project-vpc with IPV4 CIDER (10.0.0.0/16)**
#### Subnets

    To achieve high availability we have created subnets in two different zones 

    - Public-Web-Subnet-AZ1 (10.0.0.0/24)
    - Public-Web-Subnet-AZ2 (10.0.1.0/24)

    - Private-App-Subnet-AZ1 (10.0.2.0/24)
    - Private-App-Subnet-AZ2 (10.0.3.0/24)

    - Private-DB-Subnet-AZ1 (10.0.4.0/24)
    - Private-DB-Subnet-AZ2 (10.0.5.0/24)
#### Internet connectivity
To give the public subnets in our VPC internet access we have created internet gateway **(“Project-IGW”)** and   attached to our **VPC “Project-vpc”**.

#### NAT Gateway

We will use NAT Gateway for private Instances to access internet through the secure gateway channel. We have created two NAT gateways (NAT-GW-AZ1, NAT-GW-AZ2) due to multi AZ.

#### Routing Configuration  
Three dedicated route tables have been provisioned as part of the network routing setup, as outlined below.

**PublicRouteTable**
![Public-Route-Table](./images/public-rt.png)

**PrivateRouteTableAZ1**
![PrivateRouteTable-AZ1](./images/privatert-az1.png)

**PrivateRouteTableAZ2**
![PrivateRouteTable-AZ2](./images/privatert-az2.png)

#### Security Groups
We will create some Security groups which will filter the traffic be to our Elastic Load Balancers and EC2 instances. So we have created security groups as given below

    -WebTierSG
    -Privateinstances-sg
    -DB-SG
    -Internet-facing-lb-sg
    -Internal-lb-sg

## Database Deployment

  - Subnet Groups  created **“project-db-subnet-group”**
  -     • Database Deployment **“ArouraDB/MySQL”** 
RDS Writer endpoint **“database-1-instance-1.cznsyzczkkou.us-east- 1.rds.amazonaws.com”**

## App Tier Instance Deployment
  
**App Instance Deployment**

  - Launched an EC2 instance with Amazon Linux image named as App-server
  
**Connect to Instance**

  - Connected to app-server using session manager.
  - Login as ec2-user

**To Configure Database we will use the following commands**

```
-sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
-sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
-sudo yum install https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm 
-sudo yum install mysql –y

```

