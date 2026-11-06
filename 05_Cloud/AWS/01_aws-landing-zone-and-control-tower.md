# 01 – AWS Landing Zone & Control Tower

## 1. AWS Landing Zone

**Purpose:** A Landing Zone is a ready-made, secure, multi-account AWS environment that provides a foundation for an organization to run its workloads.

**Key Points:**

- Creates an account structure for teams, projects, and environments.
- Organizes security, networking, logging, and shared services.
- Applies consistent security and configuration standards across accounts.
- Uses AWS Organizations to centrally manage multiple accounts.
- Supports controlled access, governance, and compliance.
- Makes it easier to add accounts as the organization grows.

## 2. AWS Control Tower

**Purpose:** AWS Control Tower is a service that helps set up and govern a secure, multi-account AWS environment (Landing Zone).

**Key Features:**

- Automates the setup of a Landing Zone.
- Uses AWS Organizations to manage accounts and OUs.
- **Account Factory:** Standardizes provisioning of new AWS accounts.
- **Controls (Guardrails):** Help enforce and detect security and compliance requirements.
- Provides a central dashboard to monitor the environment and control compliance.
- Integrates with IAM Identity Center for centralized workforce access.
- Reduces manual account setup and governance effort.

### Types of Controls

| Type       | Purpose                                                                             |
| ---------- | ----------------------------------------------------------------------------------- |
| Preventive | Blocks specified actions that violate policies                                      |
| Detective  | Detects non-compliant resources or configurations                                   |
| Proactive  | Checks supported resources before provisioning and blocks non-compliant deployments |

## 3. Landing Zone vs Control Tower

| Landing Zone                                                 | AWS Control Tower                                     |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| Secure multi-account AWS foundation                          | AWS service used to set up and govern that foundation |
| Defines account structure, security, networking, and logging | Automates setup, account provisioning, and governance |
| Overall architecture/environment                             | Managed implementation and governance service         |

**Easy way to remember:**

- **Landing Zone = Foundation** — creates the structure and secure environment.
- **Control Tower = Manager** — helps set up, monitor, and govern the environment.

## 4. Simple Real-World Example

Imagine a company building an office campus:

- **Landing Zone:** Designs separate buildings for HR, Finance, IT, and Security, with a proper structure and common standards.
- **Control Tower:** Helps establish the campus, manage access, monitor compliance, and ensure buildings follow company policies.

In AWS, separate accounts can represent Development, Testing, Production, Security, and Shared Services.

## 5. Important Related AWS Services

| Service             | Purpose                                      |
| ------------------- | -------------------------------------------- |
| AWS Organizations   | Central multi-account management             |
| IAM Identity Center | Centralized workforce access                 |
| AWS CloudTrail      | Records AWS API activity                     |
| AWS Config          | Tracks resource configuration and compliance |
| AWS Security Hub    | Centralizes security findings                |
| Amazon GuardDuty    | Detects threats and suspicious activity      |
| AWS KMS             | Manages encryption keys                      |

## 6. Interview Questions

### A. AWS Landing Zone

1. What is an AWS Landing Zone?
2. Why do organizations use multiple AWS accounts?
3. What is AWS Organizations?
4. What is an Organizational Unit (OU)?
5. What is the purpose of Log Archive and Audit/Security accounts?
6. How would you organize Dev, Test, and Production accounts?

### B. AWS Control Tower

1. What is AWS Control Tower?
2. What is the difference between Landing Zone and Control Tower?
3. What is Account Factory?
4. What are Control Tower controls (guardrails)?
5. What is the difference between preventive, detective, and proactive controls?
6. How does Control Tower help maintain security and compliance across AWS accounts?

## Quick Revision

- **Landing Zone:** Secure, governed, multi-account AWS foundation.
- **Control Tower:** AWS service that helps set up and govern a Landing Zone.
- **Organizations:** Centrally manages AWS accounts.
- **OU:** Groups accounts for management and policy application.
- **Account Factory:** Standardized account provisioning.
- **Controls:** Help prevent, detect, or proactively block non-compliant configurations.

**Interview focus:** Explain why multiple accounts are used, how Control Tower governs them, and how controls help maintain consistent security and compliance.
