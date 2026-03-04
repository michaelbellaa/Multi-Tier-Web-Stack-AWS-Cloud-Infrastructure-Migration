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
- 
