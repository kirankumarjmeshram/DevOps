# 04 – AWS VPC (Virtual Private Cloud)

## 1. What is Amazon VPC?

**Purpose:** Amazon VPC creates an isolated virtual network in AWS for launching and connecting resources securely.

**Key Points:**
- Configure custom IP ranges using CIDR blocks.
- Divide a VPC into public and private subnets across Availability Zones.
- Control traffic using route tables, Security Groups, and Network ACLs.
- Connect to on-premises networks using VPN or AWS Direct Connect.
- Deploy resources across multiple Availability Zones for high availability.

## 2. Core VPC Components

| Component | Purpose |
|---|---|
| VPC | Logically isolated virtual network |
| CIDR block | Defines the VPC IP address range |
| Subnet | Divides a VPC IP range within one Availability Zone |
| Route Table | Determines where network traffic is routed |
| Internet Gateway (IGW) | Enables internet connectivity for resources with public addressing and suitable routes |
| NAT Gateway | Allows resources in private subnets to initiate outbound internet access |
| Security Group | Stateful firewall at network-interface/resource level |
| Network ACL (NACL) | Stateless firewall at subnet level |
| VPC Peering | Private connectivity between two VPCs |
| VPC Endpoint | Private connectivity to supported AWS services |
| VPN | Encrypted connectivity between networks over the internet |
| Direct Connect | Dedicated network connection from on-premises to AWS |

## 3. Public vs Private Subnet

| Public Subnet | Private Subnet |
|---|---|
| Route table has a route to an Internet Gateway | No direct route to an Internet Gateway |
| Instances need a public IPv4 address or other public addressing to communicate directly with the internet | Instances are not directly reachable from the internet |
| Used for internet-facing load balancers or bastion hosts | Used for application servers, databases, and internal workloads |

**Important:** A subnet is considered public because its route table has a route to an Internet Gateway. A public IP alone does not make a subnet public.

## 4. Route Tables

A route table contains rules that determine where network traffic is sent.

Example:

| Destination | Target | Purpose |
|---|---|---|
| `10.0.0.0/16` | local | Communication within the VPC |
| `0.0.0.0/0` | Internet Gateway | Internet route for a public subnet |
| `0.0.0.0/0` | NAT Gateway | Outbound internet route for a private subnet |

- Every subnet is associated with a route table; if no explicit association exists, it uses the main route table.
- The most specific matching route (longest prefix) is selected.

## 5. Internet Gateway vs NAT Gateway

| Internet Gateway | NAT Gateway |
|---|---|
| Enables internet connectivity for public IPv4 traffic | Enables outbound internet access from private subnets |
| Used by resources with public addressing and a route to IGW | Private instances do not need public IP addresses |
| Supports inbound and outbound internet communication when rules allow | Does not allow unsolicited inbound connections from the internet |

**Typical setup:** Public subnet → Internet Gateway. Private subnet → NAT Gateway in a public subnet → Internet Gateway.

## 6. Security Group vs Network ACL

| Security Group | Network ACL |
|---|---|
| Associated with network interfaces | Associated with subnets |
| Stateful | Stateless |
| Allow rules only | Allow and deny rules |
| Return traffic automatically allowed | Return traffic must be explicitly allowed |

## 7. VPC Connectivity

- **VPC Peering:** Connects two VPCs privately; transitive routing is not supported.
- **Transit Gateway:** Centrally connects multiple VPCs and on-premises networks.
- **VPC Endpoint:** Provides private access to supported AWS services without requiring an internet gateway or NAT for that service.
- **Site-to-Site VPN:** Encrypted connection between an on-premises network and AWS over the internet.
- **Direct Connect:** Dedicated private network connection between an on-premises location and AWS.

## 8. High Availability and Multi-AZ

- A VPC spans all Availability Zones in a Region.
- A subnet belongs to exactly one Availability Zone.
- Create subnets in multiple AZs for resilient architecture.
- Deploy application instances across AZs and use a load balancer.
- For higher NAT availability, use a NAT Gateway in each AZ and route each private subnet to its same-AZ NAT Gateway.

## 9. Common VPC Architecture

```text
                         Internet
                            |
                    Internet Gateway
                            |
                Public Subnet (AZ-A)
                  Load Balancer
                  NAT Gateway
                            |
             +--------------+--------------+
             |                             |
      Private Subnet (AZ-A)         Private Subnet (AZ-B)
        App Instances                 App Instances
             |                             |
             +--------------+--------------+
                            |
                    Database Subnets
                     (Private, Multi-AZ)
```

## 10. Top VPC DevOps Interview Questions

### Fundamentals
1. What is an AWS VPC?
2. What is a CIDR block?
3. What is the difference between a VPC and a subnet?
4. Can a subnet span multiple Availability Zones?
5. What is the difference between public and private subnets?
6. What is a route table?

### Networking and Security
7. What is an Internet Gateway?
8. What is a NAT Gateway, and why is it used?
9. What is the difference between an Internet Gateway and a NAT Gateway?
10. What is the difference between a Security Group and a NACL?
11. How does an EC2 instance in a private subnet access the internet?
12. How do you allow SSH access only from a specific IP address?

### Connectivity and High Availability
13. What is VPC Peering? Does it support transitive routing?
14. What is a VPC Endpoint?
15. What is the difference between VPC Peering and Transit Gateway?
16. What is the difference between Site-to-Site VPN and Direct Connect?
17. How do you design a VPC across multiple Availability Zones?
18. How do you make NAT Gateway connectivity highly available?

### Scenario-Based Questions
19. An EC2 instance in a public subnet cannot access the internet. What do you check?
20. An EC2 instance in a private subnet cannot download software updates. How do you troubleshoot?
21. An application is reachable from one subnet but not another. What do you investigate?
22. Two VPCs need private communication. Which options can you use?
23. How would you design a secure VPC for a three-tier application?
24. How would you troubleshoot a connection timeout between an application and database?

## 11. Useful VPC Concepts to Remember

- **CIDR:** Defines an IP address range, e.g. `10.0.0.0/16`.
- **Public subnet:** Has a route to an Internet Gateway.
- **Private subnet:** Has no direct route to an Internet Gateway.
- **IGW:** Internet connectivity for public resources.
- **NAT Gateway:** Outbound internet access for private resources.
- **Route Table:** Controls traffic destinations.
- **Security Group:** Stateful, network-interface-level firewall.
- **NACL:** Stateless, subnet-level firewall.
- **Multi-AZ:** Deploys resources across Availability Zones for resilience.

**Interview focus:** Be confident with CIDR, public vs private subnets, route tables, IGW vs NAT Gateway, Security Groups vs NACLs, VPC connectivity, and troubleshooting traffic flow.
