# Multi-Tier-Web-Stack-AWS-Cloud-Infrastructure-Migration
## High-Availability | Custom VPC | Route 53 Service Discovery | Security Hardening
This project demonstrates the successful migration of a 5-tier web application from a local Vagrant environment to a production-grade AWS Cloud Infrastructure.

The architecture is deployed across five Amazon EC2 instances within a custom VPC in the eu-north-1 (Stockholm) region. The primary goal was to eliminate hardcoded dependencies by implementing Route 53 Private Hosted Zones and securing the data tier using Nested Security Groups.

# The Cloud Architecture
| Tier | Component | Instance Type | OS | Role |
| :--- | :--- | :--- | :--- | :--- |
| **Web / Proxy** | Nginx | t3.micro | ubuntu-noble-24.04-amd64 | Reverse Proxy & SSL Termination |
| **App Tier** | Apache Tomcat | t3.micro | CentOS 9 x86_64 MINIMAL | Java Servlet Container |
| **Message Broker** | RabbitMQ | t3.micro | CentOS 9 x86_64 MINIMAL | Asynchronous Messaging |
| **Cache Layer** | Memcached | t3.micro | CentOS 9 x86_64 MINIMAL | Session & Query Caching |
| **Database** | MySQL | t3.micro | CentOS 9 x86_64 MINIMAL | Persistent Data Storage |

# Networking & Service Discovery
A key highlight of this migration is the transition from static /etc/hosts entries to a Cloud-Native DNS Strategy.

## VPC & Subnet Design
- Region: eu-north-1 (Stockholm)
- VPC CIDR: 172.21.0.0/16
- Connectivity: An Internet Gateway (IGW) provides public access to the Nginx Tier, while the Backend remains in private-facing subnets.

## Internal DNS (Route 53)
I configured a Private Hosted Zone (vprofile) to allow the application server to resolve backend services dynamically. This makes the infrastructure resilient; if an instance is replaced, only the Route 53 record needs updating—the application code remains untouched.
| Service | Internal DNS Record | Port |
| :--- | :--- | :--- |
| **Database** | `db01.vprofile | 3306 |
| **Message Broker** | `rmq01.vprofile | 5672 |
| **Cache Store** | `mc01.vprofile | 11211 |
| **App Server** | `app01.vprofile | 8080 |

## 🏗️ System Architecture
This project implements a secure, scalable 5-tier web application architecture on AWS. The design uses physically separated instances for each tier to enforce isolation and follows the principal of least privilege via Security Groups.

All components are deployed within a single **VPC** (Virtual Private Cloud).

---

### **Component & Infrastructure Stack**
This table outlines the specific EC2 instance type and Operating System (OS) used for each component of the stack. All instances are powered by **t3.micro** EC2 instances.

| Tier | Component | Instance Type | OS | Role |
| :--- | :--- | :--- | :--- | :--- |
| **Web / Proxy** | Nginx | t3.micro | Ubuntu 22.04 | Reverse Proxy & SSL Termination |
| **App Tier** | Apache Tomcat | t3.micro | Amazon Linux 2 | Java Servlet Container |
| **Message Broker**| RabbitMQ | t3.micro | Amazon Linux 2 | Asynchronous Messaging |
| **Cache Layer** | Memcached | t3.micro | Amazon Linux 2 | Session & Query Caching |
| **Database** | MySQL | t3.micro | Amazon Linux 2 | Persistent Data Storage |


---

### **Internal Service Discovery (Route 53)**
A key feature of this design is the use of **Route 53 Private Hosted Zones**. The App Tier is configured to connect to backend services using internal DNS records (e.g., `db01.vpro.internal`) rather than private IP addresses. This makes the architecture decoupled; instances can be replaced or updated without modifying application code.

| Service | Internal DNS Record | Port |
| :--- | :--- | :--- |
| **Database** | `db01.vpro.internal` | 3306 |
| **Message Broker** | `rmq01.vpro.internal` | 5672 |
| **Cache Store** | `mc01.vpro.internal` | 11211 |
| **App Server** | `app01.vpro.internal` | 8080 |


---

### **VPC Traffic Flow**
The architecture diagram below visualizes the networking logic and communication paths within the VPC.

1.  Traffic flows from the **Load Balancer (ELB)** to the **Application Server (EC2)**.
2.  The application uses **Route 53** to find the private IP addresses of backend services.
3.  The App server then communicates with the **Message Broker (RabbitMQ)** and the **Memcached (MC)** service.
4.  The Memcached service provides quick data access and communicates directly with the primary **Database (RDS)** for persistent storage.

```markdown
<p align="center">
  <img src="your-image-url.png" alt="Cloud Services Architecture (AWS) Diagram" />
</p>
