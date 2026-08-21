# ☁️ AWS DevOps Infrastructure Project

> **Production-style AWS infrastructure using EC2, Nginx, Apache2, HTTPS, EBS, Security Groups, and CloudWatch monitoring.**

---

## 🚀 Project Overview

This project demonstrates the deployment of a **secure and highly available web infrastructure on AWS** using multiple Ubuntu EC2 instances.

The architecture uses:

* **Nginx** as the public-facing reverse proxy and load balancer
* **Two Apache2 servers** as private backend servers
* **AWS Security Groups** to control network communication
* **HTTPS** for encrypted client-to-server communication
* **Amazon EBS** for persistent storage
* **Amazon CloudWatch** for basic infrastructure monitoring

The main goal was to understand how multiple infrastructure components work together to create a **secure, scalable, and fault-tolerant web environment**.

---

# 🏗️ Architecture

```text
                           🌐 INTERNET
                                │
                         HTTP / HTTPS
                                │
                                ▼
                 ┌─────────────────────────┐
                 │       EC2-1             │
                 │   Ubuntu + Nginx        │
                 │                         │
                 │ Reverse Proxy           │
                 │ Load Balancer           │
                 │ HTTPS / SSL             │
                 └────────────┬────────────┘
                              │
                       Private AWS Network
                              │
                  ┌───────────┴───────────┐
                  │                       │
                  ▼                       ▼
        ┌──────────────────┐   ┌──────────────────┐
        │      EC2-2       │   │      EC2-3       │
        │ Ubuntu + Apache2 │   │ Ubuntu + Apache2 │
        │                  │   │                  │
        │   Backend #1     │   │   Backend #2     │
        └──────────────────┘   └──────────────────┘

                  │
                  ▼
          ┌─────────────────┐
          │   EC2-1 EBS     │
          │   8 GiB gp3     │
          │      /data      │
          └─────────────────┘
```

---

# 🎯 Key Features

| Feature                 | Implementation               |
| ----------------------- | ---------------------------- |
| ☁️ Cloud Infrastructure | 3 Ubuntu EC2 instances       |
| 🌐 Reverse Proxy        | Nginx                        |
| ⚖️ Load Balancing       | Nginx Round-Robin            |
| 🖥️ Backend Servers     | 2 × Apache2                  |
| 🔒 Secure Communication | HTTPS / SSL                  |
| 🛡️ Network Security    | AWS Security Groups          |
| 💾 Persistent Storage   | 8 GiB gp3 EBS                |
| 📊 Monitoring           | CloudWatch + Linux utilities |
| 🔄 Failure Testing      | Backend failure simulation   |
| 📝 Documentation        | Git & GitHub                 |

---

# 🔄 Traffic Flow

The application follows this request flow:

```text
Client
  │
  │ HTTP / HTTPS
  ▼
Nginx
  │
  ├──────────────► Apache Backend 1
  │
  └──────────────► Apache Backend 2
                         │
                         ▼
                    Web Application
```

Nginx distributes incoming requests between the two Apache2 backend servers using **round-robin load balancing**.

This allows the application to continue serving traffic even if one backend server becomes unavailable.

---

# 🧩 Infrastructure Components

## 1️⃣ EC2 Infrastructure

Three Ubuntu EC2 instances were deployed:

| Instance | Role                                | Network |
| -------- | ----------------------------------- | ------- |
| EC2-1    | Nginx Reverse Proxy / Load Balancer | Public  |
| EC2-2    | Apache2 Backend Server 1            | Private |
| EC2-3    | Apache2 Backend Server 2            | Private |

---

## 2️⃣ Apache2 Backend Servers

Apache2 was installed and configured on both backend instances.

Both servers host the same custom web application.

The backend servers communicate with Nginx using their **private IP addresses**.

This prevents direct public access to the backend web servers.

---

# ⚖️ Nginx Reverse Proxy & Load Balancing

Nginx acts as the entry point for incoming web traffic.

An upstream group was configured containing both Apache2 backend servers:

```nginx
upstream apache_servers {
    server <BACKEND-1-PRIVATE-IP>;
    server <BACKEND-2-PRIVATE-IP>;
}
```

Nginx then forwards requests to the backend servers:

```nginx
location / {
    proxy_pass http://apache_servers;
}
```

### 🔁 Load Balancing Method

The project uses **round-robin load balancing**.

```text
Request 1 ──► Backend 1
Request 2 ──► Backend 2
Request 3 ──► Backend 1
Request 4 ──► Backend 2
```

This distributes incoming traffic across both backend servers.

---

# 🔐 HTTPS / SSL

HTTPS was configured on the public Nginx server using a **self-signed SSL certificate**.

```text
Client
  │
  │ HTTPS
  ▼
Nginx
  │
  │ HTTP
  ▼
Private Apache Backends
```

> ⚠️ Since this project uses a self-signed certificate without a registered domain, browsers may display a certificate warning. This is expected for the project environment.

---

# 🛡️ Security Architecture

Security Groups were configured to restrict communication between the infrastructure components.

### Public Nginx Server

Allows:

```text
HTTP  → Internet
HTTPS → Internet
SSH   → Administrator IP
```

### Private Apache Servers

Allow:

```text
HTTP → Nginx Security Group
SSH  → Restricted Administration
```

The backend servers are therefore **not directly exposed to the public internet**.

### 🔒 Security Flow

```text
                INTERNET
                    │
                    ▼
             ┌─────────────┐
             │   NGINX     │
             │ Public EC2  │
             └──────┬──────┘
                    │
             Private Network
                    │
            ┌───────┴───────┐
            ▼               ▼
        Apache #1        Apache #2
         Private          Private
```

---

# 💾 EBS Persistent Storage

An **8 GiB gp3 EBS volume** was attached to the Nginx EC2 instance.

The volume was mounted at:

```bash
/data
```

The mount configuration was added to:

```text
/etc/fstab
```

The volume UUID was used to ensure that the storage remains automatically mounted after a server reboot.

### Storage Verification

```bash
df -h
```

```bash
lsblk
```

---

# 📊 Monitoring

Basic infrastructure monitoring was performed using **Amazon CloudWatch** and Linux system utilities.

### Monitoring Commands

```bash
df -h
```

```bash
free -h
```

```bash
systemctl status nginx
```

```bash
systemctl status apache2
```

CloudWatch was used to monitor infrastructure-level metrics such as **CPU utilization**.

---

# 🧪 Failure Testing

To verify the resilience of the load-balanced architecture, one Apache backend server was intentionally stopped.

```text
Before Failure

Nginx
 ├──► Apache #1 ✅
 └──► Apache #2 ✅


After Stopping Apache #1

Nginx
 ├──► Apache #1 ❌
 └──► Apache #2 ✅
```

Traffic continued through the remaining backend server.

This demonstrated the practical benefit of having multiple backend servers behind the Nginx load balancer.

---

# ✅ Project Verification

The infrastructure was verified through multiple tests.

### EC2 Infrastructure

* 3 Ubuntu EC2 instances successfully configured
* Nginx deployed on the public-facing instance
* Apache2 deployed on both backend instances

### Nginx & Apache

* Nginx service verified as active
* Both Apache2 servers successfully served the application
* Backend communication verified through private IP addresses
* HTTP `200 OK` responses confirmed

### HTTPS

* HTTPS endpoint successfully configured
* HTTPS requests returned successful responses
* Self-signed certificate warning expected in browsers

### EBS

* 8 GiB gp3 EBS volume attached
* Volume mounted at `/data`
* Persistent mount configured using `/etc/fstab`
* Persistence verified after reboot

### Load Balancing

* Round-robin distribution configured
* Backend failure scenario tested
* Remaining backend continued serving requests

---

# 🌐 Deployed Application

A custom **DevOps Cloud web application** was deployed on both Apache2 backend servers.

Users access the application through the public Nginx endpoint.

```text
User
 │
 ▼
HTTPS
 │
 ▼
Nginx
 │
 ├──────► Apache #1
 │
 └──────► Apache #2
             │
             ▼
       DevOps Cloud Website
```

---

# 📚 What I Learned

This project provided hands-on experience with:

* ☁️ AWS EC2 infrastructure
* 🐧 Ubuntu server administration
* 🌐 Nginx reverse proxy configuration
* ⚖️ Load balancing
* 🖥️ Apache2 web server deployment
* 🔐 HTTPS / SSL configuration
* 🛡️ AWS Security Groups
* 💾 EBS persistent storage
* 📊 CloudWatch monitoring
* 🔄 Failure testing
* 🌐 Private networking
* 🔧 Linux service management
* 🐙 Git & GitHub

---

# 💡 Project Outcome

This project demonstrates how a **multi-server web architecture** can be designed and deployed using AWS infrastructure.

The implementation combines:

```text
EC2
 │
 ├── Nginx
 │     ├── Reverse Proxy
 │     ├── Load Balancer
 │     └── HTTPS
 │
 ├── Apache2 Backend #1
 │
 ├── Apache2 Backend #2
 │
 ├── Security Groups
 │
 ├── EBS
 │
 └── CloudWatch
```

It provided a strong practical foundation for understanding **AWS infrastructure, web server administration, networking, security, load balancing, and DevOps fundamentals**.

---

# 🚀 Future Improvements

The architecture can be further enhanced with:

* 🔒 Let's Encrypt SSL certificates
* 🌐 Custom domain name
* ⚖️ AWS Application Load Balancer
* 📈 Auto Scaling
* 📊 Advanced CloudWatch dashboards
* 🚨 CloudWatch alarms & SNS notifications
* 🏗️ Infrastructure as Code using Terraform
* 🔄 CI/CD pipeline
* 🐳 Containerization
* 🤖 Automated deployments

---

# 👨‍💻 Author

**Sparsh Jambhulkar**

🎓 B.Tech — Robotics & Artificial Intelligence

**Interests:**
`AWS` • `Cloud` • `DevOps` • `Linux` • `Infrastructure`

---

⭐ **If you found this project useful, consider giving the repository a star!**

> **Built as part of my hands-on journey into AWS & DevOps.**
