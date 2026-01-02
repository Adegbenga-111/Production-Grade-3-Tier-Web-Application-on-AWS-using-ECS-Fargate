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

### Step 5.

 Route Tables helps to direct Network traffic form outside the vpc to the public Subnet to the ALB and , then to the private subnet .

Public Route Table

0.0.0.0/0 → Internet Gateway ( for both ALB-Subnet A & B ) as shown below:


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(229).png)


Image 12.

Associate with both public subnets, as shown in the image below:


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(231).png)

Image 13.

Private App Route Tables

App Subnet A → 0.0.0.0/0 → NAT-A , Shown below:


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(232).png)

Image 14 : Creation of the route table.


![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(234).png)

Image 15 : 0.0.0.0/0 → NAT-A


App Subnet B → 0.0.0.0/0 → NAT-B

![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(234).png)

Image 16 : route table 0.0.0.0/0 -> NAT B

DB Route Table

No internet route

Local VPC traffic only , as shown below :

![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(278).png)

Image 17.

### Phase 2 : Create  Relational DataBase Sever And Creatiion Of The DB 

#### Step 6 :
Engine: PostgreSQL

Multi-AZ: NO ( not allowed on free t

Public access:  Yes ( to allow connenction to the DB)

Subnet group: DB subnets

SG: RDS SG
The Image below is the summary of the configuration of the DB :
 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(346).png)

Imaage 18.

#### Step 7: 
Connecting to the DB to create the db and tables with the following Pgsql command :
     

 "CREATE DATABASE testdb;

\c testdb;

CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
); "


 After trying to connecting with the server from outside the vpc, ( it failed since the subnet og the DB is private and not cooneted to a NAT gateway ) , so i had to connect through a EC2 in the same VPC . 
 The above Command was ran in the EC2 , after downloading the postgresql client  for ubuntu, As shown belown :
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(313).png)

   Image 19.

Than i ran the command , as shown belown :

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(318).png)

 Image 20: In the above image , when the command  ran it said that testdb was aleady create so i connected to it with '\c testdb' command and create the table , as shown in the lower part of the image above .  

### Phase 2 : Building and pushing of the docker image ,and running of the contianer 
#### Step 8:
Defining of the backend folder I.e part of the software that will makes the backend like the api.js , dockerfile for the building of the image , etc.  The image below shows the dockerfile and the nignx config file :

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(321).png)

 Image 21 : this shows the docker command and comments explaining each line .

  ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(323).png)

  Image 22 : This shows the conf file for nignx.

 #### Step 9 : 
 Now that all of that is set , building the image with 

 "docker build -t backend-php ."
 as shown below :
 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(332).png)

 Image 23.
After the image was done build i had to change the name to web-app:lastest (lastest was a misspell , and i just rolled with it) .

To push the image i created a repo for my images on aws , named web-app , as shown below :

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(294).png)

 Image 24.

In order to push the image , i had to set up two things 
- Install aws cli , as shown below
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(336).png)

     Image 25.
- Create a user , save the user information , and use that infromation to log in .
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(341).png)
  Image 26 : loging successfully after some back and front .
