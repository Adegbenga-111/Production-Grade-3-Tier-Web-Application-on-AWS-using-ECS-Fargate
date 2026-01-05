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
  name VARCHAR(100)
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
); "


 After trying to connecting with the server from outside the vpc, ( it failed since the subnet og the DB is private and not cooneted to a NAT gateway ) , so i had to connect through a EC2 in the same VPC . 
 The above Command was ran in the EC2 , after downloading the postgresql client  for ubuntu, As shown belown :
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(313).png)

   Image 19.

Than i ran the command , as shown belown :

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(318).png)

 Image 20: In the above image , when the command  ran it said that testdb was aleady create so i connected to it with '\c testdb' command and create the table , as shown in the lower part of the image above .  

### Phase 2 : Building The EC2 App Server 
#### Step 8:
Launching an EC2 in the App subnet in the VPC we created , The specs of the EC2 are as follows :

- OS -> Ubuntu

- Instance type -> t3.micro

-Public IP -> Disable

- Security Group -> allow ssh from anywhere . -> allow https/http from The Application Load Balancer .
  As shown in the Images below

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(367).png)

  Image 20.

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(368).png)

 Image 21.

#### Step 9:
 Now i connected to the App EC2 througth the public EC2 using the "sodu ubuntu@10.0.11.202 "
 After which i install :
 - Apache 2 with "sudo apt install Apache 2 "
 - php with pgsql with "sodu apt install  -y php -pgsql , as shown in the image below:
   
    ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(446).png)

   Image 21.
Now i had to change the permission to the var/www/html using the following command :
-  sudo chmod -R apache:apache  /var/www/html , as shown below :

    ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(448).png)

    Image 22.
- sudo  chmod -R 755 /var/www/html , as shown below :

  ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(449).png)

  Image 23.

The following command were used to allow apache to read files in that directory.

#### Step 10
create /api/save.php in the /var/www/html directory. In order to do so , i had to create with /api in /var/www/html using sudo mkdir -p /var/www/html/api , as shown below :

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(450)1.png)

   Image 24.
 Then create and put the save.php in /var/www/html/api/ using "sudo nano /var/www/html/api/save.php " command as shown below :

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(444).png)

Image 25.

### Phase 3 : S3 and Cloudfront

#### Step 11
Create s3 bucket name 'app-frontend100' , review of the configuration is shown below :

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(407).png)  

Image 26.

Then created a cloudfront distribution , update s3 policy , and change  the origin to the s3 endpoint , as shown in the images below 

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(408).png)  

   Image 26 : Creation of the cloudfront 

  ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(411).png)  

 Image 27  : updating s3 policy 

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(410).png) 

 Image 30 : Changing of the origin to the s3 endpoint .


### Phase 4 :
creating the Application Load Balancer , Add target group , setting the portocol . 
The images below shows how the ALB was configured in the order :

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(375).png) 
 
 Image 31.

  ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(376).png)  

  Image 32.

  ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(377).png)

  Image 33. 

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(378).png)  

 Image 34.

 ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(379).png)  

 Image 35.

 Then the api dns where add to the frontend in order to send the data from the frontend to the backend in json format .

### Issues Faced During the Project & How They Were Solved

##### 1. Database Connectivity Issues (EC2 → RDS)
❌ Problem
- EC2 instance could not connect to the PostgreSQL RDS instance. Connection timeouts and authentication failures occurred.

🔍 Root Cause
-RDS security group did not allow inbound traffic from the EC2 security group.
-Initial confusion around subnet routing and whether routes were required.

✅ Solution
Configured the RDS security group to allow inbound traffic on port 5432 from the EC2 security group (not from public IPs).
Verified both resources were in the same VPC.
Confirmed no route table changes were needed since traffic was within the VPC.

Lesson learned:
-Security Groups control access inside a VPC—not route tables.

#### 2. Private EC2 Accessibility (SSH Permission Denied) :
   
❌ Problem
SSH access to a private EC2 instance failed (permission denied).

🔍 Root Cause
- Attempted to SSH directly to a private IP.
- Incorrect user (ec2-user instead of ubuntu).

✅ Solution
- Used a bastion / public EC2 or session manager where applicable.
- Corrected the SSH username to ubuntu for Ubuntu AMIs.
- Ensured key pairs for both the public and private ec2 are in peagent, and allow agent forwarding in putty .

Lesson learned:
Private instances require controlled access paths.

#### 3. Application Load Balancer Target Group Not Showing Targets
❌ Problem
- Target group appeared empty even though EC2 backend was running.
🔍 Root Cause
-Backend EC2 instance was not registered with the target group.
- Health check path did not exist.
✅ Solution
- Registered the EC2 instance manually to the target group.
- Created a valid health check endpoint (e.g. /api/health.php).
- Verified security group allowed ALB → EC2 traffic.
Lesson learned:
-ALB requires both registration and successful health checks.

#### 4. Mixed Content Error (HTTPS Frontend → HTTP Backend)
❌ Problem
- Browser blocked API requests with:
  
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(400).png)

  image 36

🔍 Root Cause
- Frontend was served over HTTPS (CloudFront).
- Backend API endpoint was HTTP.

✅ Solution
- Added HTTPS listener (443) on ALB.
  
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(401).png)

  Image 37.
  
- Requested SSL certificate using ACM.
   
   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(428).png)

    Image 38

- Redirected HTTP → HTTPS.
- Updated frontend fetch() URL to HTTPS.

Lesson learned:
Modern browsers enforce strict HTTPS security.

#### 5 CORS Errors Blocking Frontend Requests
❌ Problem
- Browser blocked API calls due to CORS policy.
- OPTIONS requests failed.

🔍 Root Cause
- Backend did not handle preflight OPTIONS requests.
- Missing CORS headers in PHP API.

✅ Solution
- Added CORS headers in save.php:

   "- Access-Control-Allow-Origin
   - Access-Control-Allow-Methods
   - Access-Control-Allow-Headers "

   ![Alt aws](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(444).png)

Image 39
- Properly handled OPTIONS requests.

Lesson learned:
CORS is a browser security rule—not a backend network issue.

#### 6 API Returning 400 Errors (“Name and phone required”)
❌ Problem
- Backend rejected valid frontend submissions.
🔍 Root Cause
- JSON payload not being read correctly.
- Missing Content-Type: application/json header.

✅ Solution
-Used:
   json_decode(file_get_contents("php://input"), true);

- Ensured frontend sent JSON correctly.
Validated fields properly.

Lesson learned:
- APIs must explicitly handle JSON input.
#### 7 Database Error Without Useful Debug Information
❌ Problem
Backend returned:

   {"message":"Database error"}

with no explanation.

🔍 Root Cause
Exception details were suppressed.

✅ Solution
Added exception handling:
  
   catch (Exception $e) {
   
   echo json_encode(
   [
       
   "message" => "Database error",
   
   "error" => $e->getMessage()
    ]);
}

Lesson learned:
Controlled error visibility speeds up debugging.
