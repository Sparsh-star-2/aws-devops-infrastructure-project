# AWS DevOps Infrastructure Project

## Project Overview
This project demonstrates a production-style AWS infrastructure setup using Ubuntu EC2 instances, Nginx, Apache2, HTTPS, EBS storage, and basic monitoring.
The infrastructure is designed with Nginx acting as a reverse proxy and load balancer in front of two Apache2 backend servers running on a private network.

### Key Features

- 3 Ubuntu EC2 instances
- Nginx reverse proxy and load balancing
- 2 Apache2 backend servers
- Round-robin load balancing
- HTTPS using a self-signed SSL certificate
- Private backend access through Security Groups
- 8 GiB gp3 EBS persistent storage
- Basic system and CloudWatch monitoring
- Backend failure testing
- Custom web application deployed on Apache2
- Git-based project documentation

---

## Architecture

The architecture consists of one public-facing Nginx server and two Apache2 backend servers communicating through the private AWS network.

![AWS DevOps Infrastructure Architecture](images/architecture-diagram.png)

### Traffic Flow

Internet
   |
   | HTTP / HTTPS
   v
EC2-1 — Ubuntu + Nginx
Reverse Proxy / Load Balancer
   |
   | Private Network
   |
   +----------------------+
   |                      |
   v                      v
EC2-2                  EC2-3
Ubuntu + Apache2       Ubuntu + Apache2
Backend 1              Backend 2

EC2-1
  |
  +-- 8 GiB gp3 EBS
      /data


# Technologies

| Technology          | Purpose                           |
| ------------------- | --------------------------------- |
| AWS EC2             | Compute infrastructure            |
| Ubuntu              | Operating system                  |
| Nginx               | Reverse proxy and load balancer   |
| Apache2             | Backend web servers               |
| AWS EBS             | Persistent storage                |
| AWS Security Groups | Network access control            |
| HTTPS / SSL         | Secure web communication          |
| CloudWatch          | Basic monitoring                  |
| Git & GitHub        | Version control and documentation |
| HTML / CSS          | Website frontend                  |



# Implementation


1. EC2 Infrastructure

Three Ubuntu EC2 instances were configured:
EC2-1: Nginx reverse proxy and load balancer
EC2-2: Apache2 Backend 1
EC2-3: Apache2 Backend 2


2. Apache Backend Servers

Apache2 was installed and configured on EC2-2 and EC2-3.
Both backend servers host the same custom website and are accessible from EC2-1 through their private IP addresses.


3. Nginx Reverse Proxy & Load Balancing

Nginx was configured as a reverse proxy and load balancer.
The upstream configuration contains both Apache backend servers:
upstream apache_servers {
    server 172.31.44.92;
    server 172.31.37.154;
}
Nginx distributes incoming requests between the two backend servers using round-robin load balancing.


4. HTTPS / SSL

HTTPS was configured on Nginx using a self-signed SSL certificate.
Because the certificate is self-signed and no domain name is configured, browsers may display a certificate warning.


5. EBS Persistent Storage

An 8 GiB gp3 EBS volume was attached to EC2-1 and mounted at:
/data
The mount was configured in /etc/fstab using the volume UUID to ensure persistence after reboot.


6. Security

Security Groups were configured so that:
EC2-1 accepts HTTP and HTTPS traffic from the internet.
SSH access is restricted to the administrator's current IP.
EC2-2 and EC2-3 allow HTTP traffic only from the EC2-1 Security Group.
Backend servers are therefore not directly accessible from the public internet.


7. Monitoring & Failure Testing

Basic infrastructure monitoring was performed using:
CloudWatch CPU utilization
df -h
free -h
systemctl status nginx
systemctl status apache2
Backend failure testing was also performed by stopping one Apache server and verifying that traffic continued through the remaining backend.

# Project Verification

1. AWS Infrastructure
The AWS infrastructure consists of three Ubuntu EC2 instances configured for the Nginx reverse proxy/load balancer and two Apache2 backend servers.

2. Nginx & Apache Verification
Nginx is active and both Apache backend servers successfully return HTTP 200 responses over the private network.

3. HTTPS & Security
HTTPS is successfully configured on Nginx and the HTTPS endpoint returns an HTTP 200 response.
Note: The project uses a self-signed SSL certificate because no domain name is configured. Browser certificate warnings are therefore expected.

4. EBS Persistent Storage
The 8 GiB gp3 EBS volume is mounted at /data and persistence was verified after reboot.

# Deployed Website

A custom DevOps Cloud website was deployed on both Apache2 backend servers and is served to users through the Nginx reverse proxy.

Security Highlights
Public access is handled through Nginx.
Apache backend servers are protected from direct public HTTP access.
Backend communication uses private IP addresses.
SSH access is restricted to the administrator's IP.
HTTPS is enabled on the public Nginx endpoint.
Security Groups are used to control communication between the infrastructure components.


# Project Outcome
This project demonstrates practical experience with:

AWS EC2 infrastructure
Linux server administration
Nginx reverse proxy configuration
Load balancing
Apache web server deployment
AWS Security Groups
HTTPS / SSL configuration
EBS persistent storage
Basic monitoring
Failure testing
Git and GitHub

The project provides a practical foundation for further DevOps work involving CI/CD, infrastructure as code, containerization, and automated deployments.
