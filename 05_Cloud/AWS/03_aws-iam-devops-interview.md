# 03 – AWS IAM (Identity and Access Management)

## 1. What is AWS IAM?

**Purpose:** AWS Identity and Access Management (IAM) securely manages who can access AWS resources and what actions they can perform.

**Key Points:**
- Creates and manages users, groups, roles, and policies.
- Uses permissions to control access to AWS services and resources.
- Supports Multi-Factor Authentication (MFA) for stronger authentication.
- Provides fine-grained access control.
- Integrates with AWS services to authorize actions.
- Supports centralized access management and activity auditing through related services such as IAM Identity Center and CloudTrail.

## 2. Core IAM Components

| Component | Purpose |
|---|---|
| User | Identity for a person or application requiring long-term credentials |
| Group | Collection of IAM users; permissions can be assigned to the group |
| Role | Assumed identity that provides temporary credentials |
| Policy | JSON document defining allowed or denied actions on resources |

**Remember:**
- User = Individual identity.
- Group = Collection of users.
- Role = Temporary permissions assumed by a trusted identity.
- Policy = Permission rules.

## 3. IAM Policy

An IAM policy is a JSON document that defines permissions.

Main elements:

| Element | Meaning |
|---|---|
| `Effect` | `Allow` or `Deny` |
| `Action` | AWS actions permitted or denied |
| `Resource` | Resources the actions apply to |
| `Principal` | Identity allowed to access a resource (used in resource-based policies and trust policies) |
| `Condition` | Optional conditions for applying the policy |

Example: Allow read-only access to one S3 bucket.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Types of IAM Policies

- **AWS Managed Policy:** Created and maintained by AWS.
- **Customer Managed Policy:** Created and managed by the customer; reusable across identities.
- **Inline Policy:** Directly embedded in one user, group, or role.

## 4. Authentication vs Authorization

| Authentication | Authorization |
|---|---|
| Verifies who you are | Determines what you can do |
| Example: Sign in using credentials and MFA | Example: Permission to launch an EC2 instance |

## 5. IAM Role and Trust Policy

An IAM role provides temporary credentials to a trusted identity.

Common use cases:
- EC2 instance accessing an S3 bucket.
- Lambda function accessing DynamoDB.
- Cross-account access.
- Federated users accessing AWS accounts.

**Trust policy:** Defines who can assume the role.

**Permissions policy:** Defines what the role can do after it is assumed.

**DevOps best practice:** Attach an IAM role to EC2 or other AWS workloads instead of storing long-term access keys in code or on servers.

## 6. MFA and Root User Security

- MFA adds an extra verification step during sign-in.
- Enable MFA on the AWS account root user and privileged IAM identities.
- Avoid using the root user for daily administration.
- Do not create root access keys.
- Use least-privilege permissions.

## 7. IAM Best Practices

1. Follow the **principle of least privilege**.
2. Prefer IAM roles and temporary credentials over long-term access keys.
3. Enable MFA for privileged access.
4. Avoid using the root user for daily tasks.
5. Rotate or remove credentials when required.
6. Use groups to manage permissions for IAM users.
7. Review unused users, roles, policies, and access keys.
8. Use CloudTrail to audit AWS API activity.

## 8. IAM vs IAM Identity Center

| IAM | IAM Identity Center |
|---|---|
| Manages AWS identities and permissions such as users, roles, and policies | Centrally manages workforce access to multiple AWS accounts and supported applications |
| Controls what an identity can do | Helps users sign in and access assigned accounts and permission sets |

## 9. How IAM Works – Example

A developer needs to upload files to an S3 bucket:

1. Create or identify the developer's IAM identity.
2. Attach a policy allowing only required S3 upload actions on the required bucket.
3. Require MFA for appropriate sign-in access.
4. The developer authenticates and makes the request.
5. IAM evaluates the applicable policies and allows or denies the action.

## 10. Top IAM DevOps Interview Questions

### Fundamentals
1. What is AWS IAM, and why is it used?
2. What are IAM users, groups, roles, and policies?
3. What is the difference between an IAM user and an IAM role?
4. What is the principle of least privilege?
5. What is the difference between authentication and authorization?
6. What is MFA, and why is it important?

### Policies and Roles
7. What is an IAM policy? Explain its main elements.
8. What is the difference between AWS managed, customer managed, and inline policies?
9. What is the difference between a trust policy and a permissions policy?
10. What is the difference between identity-based and resource-based policies?
11. What happens when an explicit Deny and an Allow both apply?
12. What is an IAM role, and how does an EC2 instance use it?
13. How do you provide cross-account access using IAM roles?

### DevOps Scenarios
14. An EC2 application needs access to S3. How would you configure permissions securely?
15. A developer needs read-only access to one S3 bucket. How would you grant it?
16. How would you avoid storing AWS access keys in application code?
17. How would you audit who performed an action in an AWS account?
18. How would you troubleshoot an `AccessDenied` error?
19. How would you give a CI/CD pipeline permission to deploy AWS resources securely?
20. How would you manage access for employees across multiple AWS accounts?

## 11. Quick Revision

- **IAM:** Controls access to AWS resources.
- **User:** Identity with long-term credentials.
- **Group:** Collection of IAM users.
- **Role:** Assumed identity that provides temporary credentials.
- **Policy:** JSON document defining permissions.
- **MFA:** Adds an extra authentication factor.
- **Least Privilege:** Grant only the permissions required.
- **Trust Policy:** Defines who can assume a role.
- **CloudTrail:** Records AWS API activity for auditing.
- **IAM Identity Center:** Centralized workforce access to AWS accounts.

**Interview focus:** Understand IAM users vs roles, policy evaluation, trust vs permissions policies, least privilege, cross-account access, and secure access for EC2 and CI/CD pipelines.
