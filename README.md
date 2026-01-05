# Production-Grade 3-Tier Web Application on AWS

## Overview

This project demonstrates the design, deployment, and troubleshooting of a **production-style 3-tier web application on AWS**, focusing on **networking, security, high availability, and real-world cloud operations**.

The architecture cleanly separates responsibilities across:
- **Frontend layer**
- **Backend application layer**
- **Database layer**

AWS managed services and best practices are used throughout, including **private subnets, load balancing, IAM roles, HTTPS, monitoring, and logging**.

> **Note:** While the project title references ECS Fargate, the current implementation uses **EC2-based backend services**. The architecture is intentionally designed to support a future migration to ECS Fargate.

---

## Architecture Diagram

![Architecture Diagram](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(452).png)

---

## Architecture Summary

### Frontend Layer
- Amazon **S3** – static website hosting  
- Amazon **CloudFront** – CDN with HTTPS  
- Amazon **Route 53** – DNS management  

### Application Layer
- **Application Load Balancer (ALB)** in public subnets  
- **EC2 instances** in private application subnets  
- PHP backend API (`save.php`)  
- IAM roles for secure access  

### Database Layer
- Amazon **RDS (PostgreSQL)**  
- Private DB subnets  
- Security group restricted to application layer only  

### Networking & Security
- Custom **VPC (10.0.0.0/16)**  
- Multi-AZ public and private subnets  
- **Internet Gateway** for public access  
- **NAT Gateways (one per AZ)**  
- Least-privilege **Security Groups**  
- End-to-end **HTTPS using ACM + ALB**  

### Observability
- **VPC Flow Logs**  
- **Amazon CloudWatch** (logs & metrics)  
- **CloudWatch Alarms**  
- **Amazon SNS** notifications  

---

## Project Phases

### Phase 1: Networking (VPC Foundation)
- Created custom VPC with DNS resolution enabled  
- Designed multi-AZ subnet architecture:
  - Public subnets (ALB, NAT Gateway)
  - Private application subnets (EC2)
  - Private database subnets (RDS)
- Configured Internet Gateway
- Deployed highly available NAT Gateways
- Implemented tier-based route tables

---

### Phase 2: Database Layer (RDS)
- Deployed PostgreSQL RDS in private subnets
- Restricted DB access using security groups
- Created database and tables from EC2 within the VPC
- Gained practical experience with RDS networking and access control

---

### Phase 3: Application Layer (Backend API)
- Launched EC2 instances in private app subnets
- Installed Apache, PHP, and PostgreSQL client
- Built backend API endpoint (`/api/save.php`)
- Implemented JSON-based request handling
- Secured database connectivity via security groups

---

### Phase 4: Frontend (S3 + CloudFront)
- Hosted static frontend on S3
- Created CloudFront distribution
- Updated S3 bucket policy for CloudFront-only access
- Enforced HTTPS
- Integrated frontend with backend API through ALB

---

### Phase 5: Load Balancing & Security
- Deployed Application Load Balancer across public subnets
- Configured target groups for backend EC2
- Implemented health checks
- Enabled HTTPS listener (443) using ACM
- Redirected HTTP traffic to HTTPS

---

## Issues Faced & Solutions (Real-World Troubleshooting)

### 1. EC2 → RDS Connectivity Failure
**Problem:** EC2 could not connect to PostgreSQL RDS  

**Root Cause:**  
- RDS security group missing inbound rule  

**Solution:**  
- Allowed inbound port `5432` from EC2 security group  

**Lesson Learned:**  
Security Groups control access *within* a VPC — not route tables.

---

### 2. SSH Access to Private EC2 Failed
**Problem:** SSH connection failed  

**Root Cause:**  
- Attempted direct access to private IP  
- Incorrect SSH user  

**Solution:**  
- Used bastion/public EC2  
- Corrected user to `ubuntu`  

**Lesson Learned:**  
Private instances require controlled access paths.

---

### 3. ALB Target Group Unhealthy
**Problem:** Target group showed no healthy instances  

**Root Cause:**  
- Missing health check endpoint  

**Solution:**  
- Created `/api/health.php`  
- Verified ALB → EC2 security group rules  

**Lesson Learned:**  
ALBs require valid health check responses.

---

### 4. Mixed Content Error (HTTPS → HTTP)
**Problem:** Browser blocked API requests  

**Root Cause:**  
- HTTPS frontend calling HTTP backend  

**Solution:**  
- Added HTTPS listener on ALB  
- Issued ACM certificate  
- Redirected HTTP → HTTPS  

**Lesson Learned:**  
Modern browsers strictly enforce HTTPS.

---

### 5. CORS Errors
**Problem:** API calls blocked by browser  

**Root Cause:**  
- Missing CORS headers  
- OPTIONS preflight not handled  

**Solution:**  
- Added CORS headers in PHP  
- Handled OPTIONS requests  

**Lesson Learned:**  
CORS is a browser security feature, not a network issue.

---

### 6. API Returning 400 Errors
**Problem:** Backend rejected valid requests  

**Root Cause:**  
- JSON body not parsed correctly  

**Solution:**  
- Used `php://input`  
- Ensured `Content-Type: application/json`  

**Lesson Learned:**  
APIs must explicitly handle JSON payloads.

---

### 7. Silent Database Errors
**Problem:** API returned generic database errors  

**Root Cause:**  
- Exception messages suppressed  

**Solution:**  
- Added controlled exception handling  

**Lesson Learned:**  
Visibility speeds up troubleshooting.

---

### 8. Frontend Success Message Not Displaying
**Problem:** Backend succeeded but UI didn’t update  

**Root Cause:**  
- HTTPS and CORS issues  

**Solution:**  
- Fixed protocol mismatch  
- Parsed API response correctly  

**Lesson Learned:**  
Frontend failures can hide backend success.

---

## Final Validation Test

User data was submitted from the frontend and verified end-to-end:
- API request succeeded
- Data inserted into RDS
- Success message displayed

✅ **Result: Infrastructure works as designed**

![Final Test](https://github.com/Adegbenga-111/Production-Grade-3-Tier-Web-Application-on-AWS-using-ECS-Fargate/blob/main/Screenshot%20(451).png)

---

## Skills Demonstrated

- AWS VPC & Networking
- Load Balancing & HTTPS
- Security Groups & IAM
- RDS administration
- Cloud troubleshooting
- Monitoring & logging
- Production-style architecture design

---

## Future Improvements

- Migrate backend to **ECS Fargate**
- Add CI/CD pipeline (GitHub Actions)
- Enable RDS Multi-AZ
- Integrate AWS WAF
- Use AWS Secrets Manager for credentials

---

## Conclusion

This project demonstrates hands-on cloud engineering skills, real-world troubleshooting, and production-aware design decisions. It reflects the expectations of a **Junior Cloud Engineer / Cloud Support Engineer** and provides a strong foundation for containerized and serverless architectures.
