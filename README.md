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

## Security Implementation
Security was implemented via a Defense-in-Depth model using Security Group Nesting (referencing SG-IDs rather than CIDR blocks).

1.vprofile-web01-sg: Allows 80/443 from the Internet (0.0.0.0/0).

2.vprofile-app01-sg: Only allows traffic on port 8080 if the source is vprofile-app01-sg.

3.vpro-backend-sg: Highly restricted; only allows 3306, 5672, and 11211 if the source is vprofile-app01-sg.

4.vpro-admin-sg: SSH access is restricted to a specific Administrative IP for management.

### Project Structure
├── architecture/         # HLD Diagrams & Networking Schema

├── scripts/              # Bash automation for service provisioning

│   ├── mysql.sh          # DB setup and schema import

│   ├── rmq.sh            # RabbitMQ & Erlang installation

│   ├── tomcat.sh         # App deployment & service config

│   └── nginx.sh          # Reverse proxy setup using Route 53 endpoints

└── README.md             # Project documentation

### Execution & Deployment
1.Infrastructure: Manually provisioned VPC, Subnets, IGW, and Route Tables.

2.DNS: Established a Private Hosted Zone and mapped A-records for all 5 EC2 Private IPs.

3.Provisioning: Executed the scripts in /scripts to automate the installation of middleware on raw Linux instances.

4.Validation: Verified end-to-end connectivity using telnet and internal DNS resolution checks.
