Markdown# AWS 3-Tier High-Availability Web Application (Terraform)

Fully automated deployment of a production-grade **3-tier architecture** on AWS using Terraform:

- Multi-AZ VPC with public & private subnets  
- 2 × EC2 web servers (across 2 AZs) running Apache  
- Public Application Load Balancer (ALB)  
- Multi-AZ encrypted MySQL RDS (automatic failover)  
- NAT Gateways for outbound internet from private subnets  
- Least-privilege security groups  
- Custom user-data showing instance details on every refresh (perfect to demonstrate load balancing)

Ideal for DevOps, Cloud Engineering, or AWS Solutions Architect labs/portfolios.

## Architecture Diagram

```mermaid
flowchart TB
    subgraph Internet[Internet]
        User[Users]
    end

    subgraph Public[Public Subnets - 2 AZs]
        ALB[Application Load Balancer]
        NAT1[NAT GW AZ1]
        NAT2[NAT GW AZ2]
    end

    subgraph Private[Private Subnets - 2 AZs]
        Web1[Web Server 1<br/>AZ: us-east-1a]
        Web2[Web Server 2<br/>AZ: us-east-1b]
        RDS[(Multi-AZ MySQL RDS<br/>Primary + Standby)]
    end

    User -->|HTTP 80| ALB
    ALB --> Web1 & Web2
    Web1 & Web2 -->|MySQL 3306| RDS
    Web1 --> NAT1 --> IGW[Internet Gateway]
    Web2 --> NAT2 --> IGW
Screenshots (Real Deployment)





























DescriptionImageALB distributing traffic across AZs (refresh shows different server)ALB Load DistributionEC2 instances in two different AZsEC2 Multi-AZMulti-AZ RDS with standby replicaRDS Multi-AZApplication Load Balancer console viewALB ConsoleTerraform apply final outputTerraform Output
How to Deploy
Bashgit clone https://github.com/yourusername/aws-3tier-terraform.git
cd aws-3tier-terraform
terraform init
terraform plan
terraform apply --auto-approve   # ~8-10 minutes
After deployment, open the URL shown in the output:
texthttp://web-alb-XXXXXXXXXX.us-east-1.elb.amazonaws.com
Refresh several times → you will see traffic alternating between the two web servers in different AZs.
Cleanup (Destroy everything)
Bashterraform destroy --auto-approve
Project Structure

provider.tf      → AWS provider configuration
variables.tf     → All variables with defaults
vpc.tf           → VPC, subnets, IGW, NAT Gateways, route tables
security-groups.tf → ALB, Web, RDS security groups
ec2.tf           → Web servers + user-data script
alb.tf           → ALB, target group, listener
rds.tf           → Multi-AZ MySQL RDS
outputs.tf       → Website URL, RDS endpoint, instance details
user-data.sh     → Bootstrap script for Apache + dynamic page

Requirements

AWS account (ALB creation enabled – new accounts may need a quick limit request)
Terraform ≥ 1.5
AWS CLI configured

100% automated, production-ready, and perfect for resumes or coursework!
