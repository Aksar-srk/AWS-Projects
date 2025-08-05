# 🏗️ Three-Tier WordPress Deployment on AWS

This project showcases the deployment of a **high-availability WordPress website** using the **Three-Tier Architecture** model on AWS. The infrastructure includes a **Load Balancer**, **Auto-Scaling EC2 instances**, **RDS for MySQL**, and **EFS for shared storage**, all managed across public and private subnets.

> 📂 All screenshots referenced in this documentation are stored in the `images/` folder.

---

## 📋 Project Overview Table

| Phase | AWS Services Used | Description |
|-------|-------------------|-------------|
| Phase 1 | VPC, Subnets, IGW, Route Tables | Setup network layer for high availability |
| Phase 2 | EC2 (Bastion, App), Security Groups | Configure bastion host and WordPress EC2 instances |
| Phase 3 | RDS (MySQL) | Setup backend database in private subnet |
| Phase 4 | Load Balancer, Target Groups | Enable HA and distribute traffic |
| Phase 5 | EFS | Shared file system between WordPress instances |
| Phase 6 | Cleanup | Remove all resources to avoid billing |

---

## 🖼️ Architecture Diagram

![Architecture Diagram](images/three-tier-architecture.png)

---

## 📌 Phase 1: VPC and Networking Setup

We created a custom VPC named `wildpress-vpc` with the following components:

- **Subnets:**
  - `wildpress-public-a` (10.0.1.0/24)
  - `wildpress-public-b` (10.0.2.0/24)
  - `wildpress-private-a` (10.0.3.0/24)
  - `wildpress-private-b` (10.0.4.0/24)
- **Internet Gateway:** `wildpress-igw`
- **Route Tables:** Configured for public and private subnets

📸 Screenshot:
![VPC Setup](images/vpc-setup.png)

---

## 🖥️ Phase 2: EC2 Instances & Security

We launched 3 EC2 instances:

- **Bastion Host**: `wildpress-bastion` (Public Subnet `wildpress-public-a`)
- **App Servers**: 
  - `wildpress-app-a` (Private Subnet `wildpress-private-a`)
  - `wildpress-app-b` (Private Subnet `wildpress-private-b`)

🔐 Security Groups:

- `bastion-sg`: SSH from your IP
- `app-sg`: HTTP from Load Balancer, SSH from Bastion
- `efs-sg`: Access from App SG

📸 Screenshots:
![EC2 Launch](images/ec2-launch.png)
![Security Groups](images/security-groups.png)

---

## 🛢️ Phase 3: RDS (MySQL)

- Launched an RDS MySQL instance named `wildpress-db`
- Deployed in private subnets with Multi-AZ disabled (Free Tier)
- Connected to EC2 via internal DNS

📸 Screenshot:
![RDS Setup](images/rds-setup.png)

---

## ⚖️ Phase 4: Load Balancer

- Created an **Application Load Balancer (ALB)** named `wildpress-alb`
- Target group includes `wildpress-app-a` and `wildpress-app-b`
- Listener on port 80 forwards requests to the target group

📸 Screenshot:
![ALB Setup](images/alb-setup.png)

---

## 📂 Phase 5: EFS (Elastic File System)

- Created `wildpress-efs` for shared storage
- Attached to both App EC2s using NFS mounting in `/var/www/html`
- Ensured PHP, Apache, WordPress installed and mounted correctly

📸 Screenshot:
![EFS Setup](images/efs-setup.png)

---

## 🧹 Phase 6: Cleanup

To avoid ongoing charges, we deleted all resources:

- EC2 instances, Key pairs
- ALB, Target Groups, Security Groups
- EFS and mount targets
- RDS instance
- Custom VPC and all associated networking components

📸 Screenshot:
![Cleanup](images/cleanup.png)

---

## 🎯 Key Takeaways

- Hands-on experience with AWS networking, EC2, RDS, EFS, and Load Balancing.
- Implemented real-world three-tier architecture with shared storage and private subnets.
- Managed IAM, SSH access, WordPress setup, Apache and PHP configurations.

---

## 📢 About This Project

This project highlights **cloud architecture skills**, **security best practices**, and **scalability** using AWS core services. Designed and implemented from scratch using the AWS Free Tier.

Feel free to check out the screenshots and replicate this architecture to practice AWS deployment and management skills.
