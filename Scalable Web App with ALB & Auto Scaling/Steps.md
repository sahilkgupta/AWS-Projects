# 🚀 Scalable Web Application Deployment on AWS

This project involves deploying a **highly available and scalable web application** using **Amazon EC2**, **Application Load Balancer (ALB)**, and **Auto Scaling**.

The goal is to ensure that the web app can **automatically scale** based on traffic demand while maintaining **high availability** and **fault tolerance** across multiple Availability Zones (AZs).

---

## 🧱 Architecture Overview

- **EC2 Instances** — Host the web application  
- **Launch Template** — Defines the instance configuration  
- **Auto Scaling Group** — Automatically adjusts instance count  
- **Application Load Balancer (ALB)** — Distributes incoming traffic evenly  
- **VPC + Subnets** — Ensure multi-AZ deployment for redundancy  

---

## ⚙️ Steps to Deploy a Scalable Web App

### **Step 1: Create a Launch Template**

1. Go to **AWS Console → EC2 → Launch Templates → Create Launch Template**  
2. Configure the following:
   - **Name:** `WebAppTemplate`  
   - **AMI:** Amazon Linux 2 or Ubuntu  
   - **Instance Type:** `t2.micro` (Free Tier) or `t3.medium`  
   - **Key Pair:** Select existing or create a new one  
   - **Security Group:** Allow **SSH (22)**, **HTTP (80)**, and **HTTPS (443)**  

3. **User Data** (optional — auto install Apache):
   ```bash
   #!/bin/bash
   sudo yum update -y
   sudo yum install httpd -y
   sudo systemctl start httpd
   sudo systemctl enable httpd
   echo "<h1>Welcome to Scalable Web App</h1>" | sudo tee /var/www/html/index.html
Click Create Launch Template

Step 2: Create an Auto Scaling Group
Go to EC2 → Auto Scaling Groups → Create Auto Scaling Group

Choose Launch Template: WebAppTemplate

Configure Group Size:

> Desired Capacity: 2

> Minimum Instances: 1

> Maximum Instances: 4

Select Network:

> Choose an existing VPC

> Select at least two subnets across different AZs

Attach Load Balancer:

> Choose Application Load Balancer (ALB)

Create Target Group:

> Target Type: Instance

> Protocol: HTTP

Health Check Path: /

Register instances later (Auto Scaling will manage this)

(Optional) Configure Scaling Policies:

Scale Out: CPU > 60%

Scale In: CPU < 40%

Click Create Auto Scaling Group

Step 3: Create an Application Load Balancer (ALB)
Go to EC2 → Load Balancers → Create Load Balancer

Select Application Load Balancer

Configure:

Name: WebAppALB

Scheme: Internet-facing

VPC: Same as Auto Scaling Group

Availability Zones: Choose at least 2

Configure Listeners:

Protocol: HTTP

Port: 80

Target Group:

Select existing Target Group from Auto Scaling Group

Security Group:

Allow HTTP (80)

Click Create Load Balancer

Step 4: Test the Setup
Get the ALB DNS Name:

Go to EC2 → Load Balancers → Copy the ALB DNS Name

Open a browser and visit:

cpp
Copy code
http://<your-alb-dns-name>
You should see:

css
Copy code
Welcome to Scalable Web App
Step 5: Verify Auto Scaling
1. Increase Load to Trigger Scaling
Use Apache Benchmark to simulate load:

bash
Copy code
ab -n 1000 -c 50 http://<your-alb-dns-name>/
Check EC2 → Auto Scaling Group to confirm that new instances are launched when traffic increases.

2. Simulate Failure
Manually stop an instance and observe Auto Scaling automatically launch a new one.

✅ Conclusion

This setup ensures:

High Availability using ALB

Scalability using Auto Scaling Group

Redundancy across multiple Availability Zones

This architecture is commonly used in production-grade applications that require:

Reliability

Fault tolerance

Cost optimization

🏗️ Architecture Diagram (Conceptual)

           ┌──────────────────────────────┐
           │      Application Load        │
           │       Balancer (ALB)         │
           └──────────────┬───────────────┘
                          │
        ┌─────────────────┴──────────────────┐
        │                                    │
┌──────────────┐                    ┌──────────────┐
│  EC2 Instance │  (AZ-1)           │  EC2 Instance │  (AZ-2)
│ (Web Server)  │                   │ (Web Server)  │
└──────────────┘                    └──────────────┘
        │                                    │
        └──────────────┬─────────────────────┘
                       │
               Auto Scaling Group
               
🧩 Future Enhancements
Add HTTPS Listener with SSL Certificate

Deploy using Terraform or CloudFormation

Integrate CloudWatch Alarms for real-time monitoring

Add CI/CD pipeline for automated deployment

📜 License
Licensed under the MIT License — free for educational and personal use.
