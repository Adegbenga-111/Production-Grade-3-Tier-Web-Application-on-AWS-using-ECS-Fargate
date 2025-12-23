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


 As shown in the image below :
  
