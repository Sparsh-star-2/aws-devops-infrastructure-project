# AWS DevOps Infrastructure Project

A hands-on AWS DevOps project demonstrating a highly available web application architecture using Amazon EC2, Ubuntu, Nginx, Apache2, EBS, load balancing, Security Groups, HTTPS, and basic monitoring.

## Architecture

Internet
   |
   | HTTP / HTTPS
   v
EC2-1 — Nginx Reverse Proxy / Load Balancer
   |
   |----------------------|
   |                      |
   v                      v
EC2-2 — Apache2       EC2-3 — Apache2
Backend 1             Backend 2

## AWS Infrastructure

| Component | Configuration |
|---|---|
| EC2-1 | Ubuntu + Nginx |
| EC2-2 | Ubuntu + Apache2 |
| EC2-3 | Ubuntu + Apache2 |
| Load Balancing | Nginx Round Robin |
| Storage | 8 GiB gp3 EBS |
| Monitoring | CloudWatch + Linux system monitoring |
| HTTPS | Nginx + self-signed SSL certificate |

## Technologies Used

- AWS EC2
- Ubuntu Linux
- Nginx
- Apache2
- Nginx Reverse Proxy
- Load Balancing
- Amazon EBS
- AWS Security Groups
- HTTPS / SSL
- CloudWatch
- Linux system administration
- Bash

## Project Implementation

### 1. EC2 Infrastructure

Created three Ubuntu EC2 instances:

- EC2-1 — Nginx load balancer
- EC2-2 — Apache backend server 1
- EC2-3 — Apache backend server 2

All instances were deployed inside the same VPC.

### 2. Apache Backend Servers

Apache2 was installed and configured on EC2-2 and EC2-3.

Both servers host the same web application from:

`/var/www/html/index.html`

The website title is:

**DevOps Cloud**

### 3. Nginx Load Balancing

Nginx was configured as a reverse proxy and load balancer.

The upstream configuration uses both Apache servers:

```nginx
upstream apache_servers {
    server 172.31.44.92;
    server 172.31.37.154;
}


4. Load Balancing Verification

Load balancing was verified using repeated requests:

for i in {1..10}; do
    curl -s http://localhost
    echo
done

5. Failure Testing

Apache was stopped on one backend server:

sudo systemctl stop apache2

Traffic continued to reach the remaining backend server.

Apache was then restarted and verified:

sudo systemctl start apache2

This demonstrated backend failure handling.

6. EBS Storage

An additional 8 GiB gp3 EBS volume was attached to EC2-1.

The volume was formatted using ext4 and mounted at:

/data

A test file was created:

echo "EBS storage test" | sudo tee /data/test.txt

EBS persistence was verified after reboot using:

df -h /data
cat /data/test.txt

The filesystem was configured in /etc/fstab using its UUID so that it automatically mounts after reboot.

7. Basic Monitoring

Basic system monitoring was performed using:

df -h
free -h
sudo systemctl status nginx
sudo systemctl status apache2

EC2 CPU utilization was also monitored through Amazon CloudWatch.

8. Security Improvements

Security Groups were configured so that:

Nginx accepts HTTP traffic from the internet.
Apache servers accept HTTP traffic only from the Nginx Security Group.
SSH access is restricted to the administrator's current IP address.

Direct HTTP access to both Apache servers from the internet was tested and blocked successfully.

9. HTTPS

HTTPS was configured on Nginx using a self-signed SSL certificate.

Nginx listens on:

443

HTTPS was verified successfully with:

curl.exe -k -I https://<Nginx-Public-IP>

The server returned:

HTTP/1.1 200 OK

Because this project does not use a registered domain, a self-signed certificate was used for demonstration purposes.

Verification

The following functionality was successfully tested:

Nginx service availability
Apache backend availability
Nginx round-robin load balancing
Backend failure handling
Website availability
EBS persistence after reboot
CPU and memory monitoring
Disk usage monitoring
Security Group restrictions
Direct backend access blocking
HTTPS connectivity
Key Learning Outcomes

This project provided hands-on experience with:

AWS EC2 infrastructure
Linux server administration
Nginx reverse proxy configuration
Load balancing
Apache web server deployment
Private IP communication
AWS Security Groups
EBS storage and persistence
Basic CloudWatch monitoring
HTTPS and SSL configuration
Service troubleshooting
High-availability concepts
Future Improvements

Potential future improvements include:

Route 53 domain configuration
Trusted SSL certificate using Let's Encrypt or AWS Certificate Manager
Application Load Balancer
Auto Scaling
CloudWatch alarms
Centralized logging
Infrastructure as Code using Terraform
CI/CD pipeline using GitHub Actions
Project Status

Completed

Core infrastructure, load balancing, failure testing, persistent EBS storage, security improvements, monitoring, and HTTPS have been implemented and verified.


