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


