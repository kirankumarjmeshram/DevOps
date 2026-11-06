# 02 – AWS EC2 Fundamentals & DevOps Interview Notes

## 1. What is Amazon EC2?

**Purpose:** Amazon Elastic Compute Cloud (EC2) provides scalable virtual servers in the cloud for running applications.

**Key Points:**
- Configurable compute resources: CPU, memory, storage, and networking.
- Pricing options include On-Demand, Reserved Instances, and Spot Instances.
- Auto Scaling adjusts the number of instances based on demand.
- Supports Linux, Windows, and custom AMIs.
- Integrates with VPC, S3, and IAM for networking, storage, and access control.

## 2. EC2 Core Concepts

| Concept | Purpose |
|---|---|
| Instance | Virtual server in AWS |
| AMI | Template used to launch EC2 instances |
| Instance type | Defines compute, memory, storage, and network capacity |
| Key pair | Authenticates SSH access to Linux instances |
| Security Group | Stateful virtual firewall for an instance/network interface |
| EBS | Persistent block storage |
| Instance Store | Temporary host-attached storage |
| User Data | Runs startup scripts during instance launch |
| IAM Role | Gives applications on EC2 temporary AWS permissions |
| Elastic IP | Static public IPv4 address |

## 3. Instance Types

| Family | Use |
|---|---|
| General Purpose (T, M) | Balanced workloads |
| Compute Optimized (C) | CPU-intensive workloads |
| Memory Optimized (R, X) | Memory-intensive workloads |
| Storage Optimized (I, D) | High storage throughput / IOPS |
| Accelerated Computing (P, G) | GPU and specialized workloads |

## 4. Instance Lifecycle

- **Pending:** Instance is launching.
- **Running:** Instance is active.
- **Stopping / Stopped:** Instance is shutting down / shut down.
- **Shutting-down / Terminated:** Instance is being deleted / deleted.

| Action | Meaning |
|---|---|
| Reboot | Restarts the operating system |
| Stop | Shuts down an EBS-backed instance; it can generally be started again |
| Terminate | Deletes the instance; root EBS volume is deleted by default when `DeleteOnTermination` is enabled |

**Remember:** EBS data is generally retained when an instance stops. Instance Store data is temporary and can be lost when the instance stops or terminates. EBS storage can still incur charges while the instance is stopped.

## 5. AMI (Amazon Machine Image)

**AMI:** A template used to launch EC2 instances. It specifies the operating system and launch configuration, including root volume and block device mappings.

**Create a custom AMI:**
1. Launch and configure an instance.
2. Install required packages and application dependencies.
3. Create an image from the instance.
4. Launch new instances using that AMI.

**Use case:** Create consistent application servers across Dev, QA, and Production.

## 6. EC2 Storage

| EBS | Instance Store |
|---|---|
| Persistent block storage | Temporary block storage |
| Network-attached | Physically attached to host |
| Can generally be detached and reattached | Tied to the host lifecycle |
| Supports snapshots | No EBS-style snapshots |
| Suitable for OS and persistent data | Suitable for cache and temporary data |

**EBS volume types:** General Purpose SSD (gp3/gp2), Provisioned IOPS SSD (io1/io2), Throughput Optimized HDD (st1), Cold HDD (sc1).

## 7. Networking and Security

### Security Group vs Network ACL

| Security Group | Network ACL |
|---|---|
| Applies to network interfaces | Applies to subnets |
| Stateful | Stateless |
| Allow rules only | Allow and deny rules |
| Return traffic automatically allowed | Return traffic must be explicitly allowed |

**Common ports:** SSH 22, HTTP 80, HTTPS 443, RDP 3389.

**Best practice:** Allow SSH/RDP only from trusted IPs; avoid `0.0.0.0/0` for management access.

### Connecting to EC2

```bash
chmod 400 my-key.pem
ssh -i my-key.pem ec2-user@PUBLIC_IP
```

Common usernames:
- Amazon Linux: `ec2-user`
- Ubuntu: `ubuntu`

**Private EC2 access:** Use Systems Manager Session Manager, a bastion host, VPN, or an EC2 Instance Connect Endpoint where supported.

## 8. User Data, Metadata, and IAM Role

### User Data
Runs startup scripts when an instance launches. Linux example:

```bash
#!/bin/bash
dnf install -y nginx
systemctl enable nginx
systemctl start nginx
```

### Instance Metadata
Provides information about the running instance. IMDSv2 uses a session token.

```bash
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/instance-id
```

### IAM Role
Allows applications on EC2 to access AWS services using temporary credentials. Prefer IAM roles over storing long-term access keys on instances.

## 9. High Availability and Scaling

- **Auto Scaling Group (ASG):** Maintains and scales a group of EC2 instances.
- **Minimum capacity:** Lowest number of instances to maintain.
- **Desired capacity:** Target number of instances.
- **Maximum capacity:** Highest number of instances allowed.
- **Launch Template:** Defines instance launch settings such as AMI, instance type, and security groups.
- **Elastic Load Balancing (ELB):** Distributes incoming traffic across targets.

**Scaling types:**
- Vertical scaling: Increase or decrease instance size.
- Horizontal scaling: Add or remove instances.

## 10. Monitoring and Troubleshooting

**Amazon CloudWatch:** Monitors EC2 metrics, logs, and alarms.

Common metrics:
- CPU utilization
- Network in/out
- Status check results
- Disk and memory usage (use CloudWatch Agent for OS-level memory and filesystem metrics)

**EC2 status checks:**
- System status check: AWS host infrastructure issue.
- Instance status check: Operating system or instance-level issue.
- Attached EBS status check: EBS connectivity or I/O issue.

### Useful Linux Commands

| Purpose | Command |
|---|---|
| CPU/processes | `top` |
| Memory | `free -h` |
| Filesystem space | `df -h` |
| Block devices | `lsblk` |
| IP addresses | `ip addr` |
| Listening ports | `ss -tulnp` |
| Service status | `systemctl status nginx` |
| Service logs | `journalctl -u nginx` |
| OS details | `cat /etc/os-release` |
| Directory size | `du -sh *` |

## 11. Top EC2 DevOps Interview Questions

### Fundamentals
1. What is EC2, and when would you use it?
2. What are EC2 instance families?
3. What is an AMI? How do you create a custom AMI?
4. What is the difference between Reboot, Stop, and Terminate?
5. What happens to EBS and Instance Store data when an instance stops or terminates?

### Storage, Networking, and Security
6. What is the difference between EBS and Instance Store?
7. What is the difference between a Security Group and a Network ACL?
8. How do you connect securely to a Linux EC2 instance?
9. How do you access an EC2 instance in a private subnet?
10. Why should you attach an IAM role instead of storing AWS access keys on EC2?
11. What is the difference between User Data and Instance Metadata?
12. What is IMDSv2?

### Automation, Scaling, and Monitoring
13. How do you install an application automatically at instance launch?
14. How do you attach and mount an additional EBS volume?
15. How do you increase an EBS volume?
16. What is an Auto Scaling Group? Explain minimum, desired, and maximum capacity.
17. How do you monitor EC2 CPU, memory, and disk usage?
18. What are EC2 status checks?

### Scenario-Based Questions
19. An EC2 instance is running, but SSH is failing. How do you troubleshoot?
20. An application on EC2 is not accessible over HTTP/HTTPS. What do you check?
21. CPU utilization suddenly reaches 100%. How do you investigate?
22. The root filesystem is full. How do you identify and fix the issue?
23. A private EC2 instance needs software updates. How can it access required repositories?
24. An instance becomes unhealthy. How do you recover it and reduce downtime?
25. How would you create consistent EC2 servers for Dev, QA, and Production?

## 12. EC2 Pricing Models

| Model | Use |
|---|---|
| On-Demand | Flexible usage without a long-term commitment |
| Reserved Instances | Discount for a one-year or three-year commitment to specified usage attributes |
| Spot Instances | Uses spare AWS capacity at a discount; can be interrupted by AWS |

## 13. Interview Preparation Priority

1. EC2 basics, AMI, and instance lifecycle.
2. EBS vs Instance Store.
3. Security Group vs Network ACL.
4. SSH and private-instance access.
5. User Data and IAM roles.
6. Auto Scaling and Load Balancers.
7. CloudWatch and status checks.
8. Scenario-based troubleshooting.

**Interview tip:** For each topic, prepare a short definition, a real-world use case, and practical steps or commands. Prioritize troubleshooting and automation scenarios.
