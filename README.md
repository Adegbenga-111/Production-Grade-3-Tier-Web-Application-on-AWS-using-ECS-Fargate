# Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate

## Introduction 

This project demonstrates the deployment of a production-grade 3-tier web application architecture on AWS, designed with scalability, security, and high availability in mind.

The solution consists of a static frontend hosted on Amazon S3, a containerized backend running on Amazon ECS (Fargate) behind an Application Load Balancer, and a managed SQL database on Amazon RDS, all deployed within a custom multi-AZ VPC.

The architecture incorporates Auto Scaling, IAM roles, private subnets, NAT Gateways, VPC Flow Logs, Amazon CloudWatch, and Amazon SNS to reflect real-world, enterprise-ready cloud infrastructure practices.

#### Note : This project will be in Phases 

### Phase 1 :Networking (VPC Foundation)
### Step 1

Create VPC

CIDR block: 10.0.0.0/16

Enable:

DNS resolution

DNS hostnames

Name: my-vpc

As shown in the Image below :

![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(213).png)

Image 02.


### Steps 2 

Create Subnets (Multi-Availability Zone (AZ) ): The reason for this is to ensure high availability of the web app. 

Public Subnets:

Public Subnet A (AZ-A): 10.0.1.0/24, as shown below :


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(214).png)

Image 3: The AZ of the Subnet and some Other setting put in place.


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(225).png)

Image 4: Enabling Auto-assign public IPv4.

Public Subnet B (AZ-B): 10.0.2.0/24


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(215).png)

Image 4: The AZ of the Subnet and some Other setting put in place.

Enable Auto-assign public IPv4 to ensure that the subnet is reachable through the internet . 


Private App Subnets

Private -Subnet-App A: 10.0.11.0/24

![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(216).png)

Image 5.

Private -Subnet-App B: 10.0.12.0/24


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(217).png)

Image 6.

Private DB Subnets

Private DB  A: 10.0.21.0/24


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(218).png)

Image 7.

Private DB  B: 10.0.22.0/24


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(219).png)

Image 8.

### Step 3 

Internet Gateway

Create and attach IGW to the VPC

Name: IGW ; As shown in the images below :


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(220).png)

Image 9: creation of the  Internet Gateway.


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(221).png)

Image 10: Attaching the Internet Gateway to the vpc .

### Step 4 

NAT Gateways (High Availability)

Allocate 2 Elastic IPs, In order to do this , on the vpc page go to " Elastic Ip" to create the two Elastic ip in the AZ.

Create:

NAT-A in Public Subnet A ,

NAT-B in Public Subnet B As shown in the image below :


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(227).png)

Image 11: This is for NAT B , but it is the same process for NAT A just that the Elastic IP that is different. 


This ensures private subnets in each AZ have internet access without cross-AZ dependency.

