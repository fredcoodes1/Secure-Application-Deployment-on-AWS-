# Secure Application Deployment on AWS


This project documents how I identified security vulnerabilities in an existing AWS hosted application and redesigned the architecture to follow AWS security best practices, including network segmentation, least privilege access, and defense in depth.

Through this project you can see the tools I used to achieve this 

---

## Initial Security Issues Identified

The original application contained multiple critical security flaws:

- EC2 instance deployed in a public subnet with SSH (22) and HTTP (80) open to `0.0.0.0/0`
- RDS database storing personal user data was publicly accessible
- Weak database credentials with no MFA
- No encryption enabled for data at rest
- Publicly accessible S3 bucket due to misconfiguration

These issues exposed the application to unauthorized access, data breaches, and undetected attacks.

---

## Secure Architecture Design

### Virtual Private Cloud (VPC)
- Custom VPC with CIDR block `10.0.0.0/16`
- Designed for scalability, isolation, and multi-AZ deployment

### Network Segmentation
- Public subnets: Application Load Balancer and Bastion Host
- Private subnets: Application EC2 instances and RDS database
- Subnets deployed across two Availability Zones for high availability

### Traffic Control
- Internet Gateway for public subnets
- NAT Gateway for outbound internet access from private subnets
- Route tables configured to restrict direct internet access to private resources

---

## Application Security

### Compute Layer
- Application EC2 instances deployed in private subnets
- No public IP addresses assigned
- Access restricted via:
  - Application Load Balancer (application traffic)
  - Bastion Host (administrative access)

### Load Balancing
- Application Load Balancer deployed in public subnets
- Forwards traffic securely to private EC2 instances

---

## Data Layer Security

### RDS
- Deployed in private subnets
- Public access disabled
- Encryption at rest enabled
- Security groups restrict access to application EC2 instances only
- Multi-AZ enabled for high availability

### S3
- Bucket configured as private
- Public access blocked
- Access controlled using IAM roles and policies

---

## Additional Security Controls

- AWS Web Application Firewall (WAF) integrated with the Application Load Balancer
- AWS Managed Rule Groups enabled to protect against common web exploits
- Network isolation ensures backend resources are not directly reachable from the internet

- Debugging & Lessons Learned

During testing, the Application Load Balancer was initially unreachable due to misconfigured security group rules. Correcting these rules restored access and reinforced the value of using Infrastructure as Code (Terraform) to avoid manual configuration errors and improve consistency.

---

## Outcome

The redesigned architecture:
- Eliminates unnecessary public exposure
- Protects sensitive user data
- Improves availability and fault tolerance
- Follows AWS Well-Architected security best practices

---

## Skills Demonstrated

- AWS Networking (VPC, Subnets, Route Tables)
- Cloud Security & Hardening
- Load Balancing & High Availability
- Defense-in-Depth Architecture
- Secure Application Deployment

