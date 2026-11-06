# 03 – AWS IAM (Identity and Access Management)

## 1. What is AWS IAM?

**Purpose:** AWS IAM securely manages who can access AWS resources and what actions they can perform.

**Key Points:**

- Creates and manages users, groups, roles, and policies.
- Controls access to AWS services and resources.
- Supports Multi-Factor Authentication (MFA).
- Provides fine-grained permissions.
- Integrates with AWS services to authorize actions.
- CloudTrail records AWS API activity for auditing.

## 2. Core IAM Components

| Component | Purpose                                                           |
| --------- | ----------------------------------------------------------------- |
| User      | Identity with long-term credentials                               |
| Group     | Collection of IAM users; permissions can be assigned to the group |
| Role      | Assumed identity that provides temporary credentials              |
| Policy    | JSON document defining permissions                                |

**Remember:** User = identity · Group = users · Role = temporary access · Policy = permission rules.

## 3. IAM Policy

An IAM policy is a JSON document that defines permissions: what actions are allowed or denied, on which resources, and under what conditions.

### Policy types

- **AWS Managed Policy:** Created and maintained by AWS.
- **Customer Managed Policy:** Created and managed by you; reusable across identities.
- **Inline Policy:** Embedded directly in one user, group, or role.

### Identity-based vs Resource-based policies

| Identity-based policy              | Resource-based policy                                    |
| ---------------------------------- | -------------------------------------------------------- |
| Attached to a user, group, or role | Attached to a resource                                   |
| Defines what the identity can do   | Defines who can access the resource and what they can do |
| Does not contain `Principal`     | Usually contains `Principal`                           |

## 4. IAM Policy JSON Structure

### Example: Allow read access to one S3 bucket

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowReadCompanyData",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::company-data",
        "arn:aws:s3:::company-data/*"
      ]
    }
  ]
}
```

### Important JSON keys

| Key              | Purpose                                               | Common values / notes                                                                                    |
| ---------------- | ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `Version`      | Policy language version                               | Usually `2012-10-17`; not the policy creation date                                                     |
| `Statement`    | Contains one or more permission statements            | JSON object or array of objects                                                                          |
| `Sid`          | Optional statement identifier                         | Any meaningful string, e.g. `AllowS3Read`                                                              |
| `Effect`       | Permission decision                                   | `Allow` or `Deny`                                                                                    |
| `Action`       | AWS API operations                                    | e.g. `s3:GetObject`, `ec2:StartInstances`, `s3:*`                                                  |
| `NotAction`    | Actions excluded from the statement                   | Used instead of `Action`; use carefully                                                                |
| `Resource`     | Resources the actions apply to                        | ARN or `"*"` where supported                                                                           |
| `NotResource`  | Resources excluded from the statement                 | Used instead of `Resource`; use carefully                                                              |
| `Principal`    | Who is allowed to access a resource                   | Used in resource-based policies and role trust policies, not ordinary identity-based permission policies |
| `NotPrincipal` | Principals excluded from a resource-based statement   | Advanced; use carefully                                                                                  |
| `Condition`    | Conditions that must match for the statement to apply | Uses condition operators and context keys                                                                |

### Key values and examples

**Effect**

```json
"Effect": "Allow"
```

- `Allow` grants the requested permission, if no overriding restriction applies.
- `Deny` explicitly blocks the requested permission.
- An applicable explicit Deny overrides an Allow.

**Action**

```json
"Action": ["s3:GetObject", "s3:PutObject"]
```

- `s3:GetObject` – read an object.
- `s3:PutObject` – upload an object.
- `s3:ListBucket` – list objects in a bucket.
- `ec2:StartInstances` – start EC2 instances.
- `s3:*` – all S3 actions.
- `"*"` – all actions; highly privileged and usually unsafe.

**Resource and ARN**

ARN (Amazon Resource Name) identifies an AWS resource.

General format:

```text
arn:partition:service:region:account-id:resource
```

Examples:

```text
arn:aws:s3:::company-data
arn:aws:s3:::company-data/*
arn:aws:ec2:ap-south-1:123456789012:instance/i-0123456789abcdef0
```

- `arn:aws:s3:::company-data` – the bucket itself.
- `arn:aws:s3:::company-data/*` – objects inside the bucket.
- `"*"` – all resources supported by the specified action; some actions require `"*"`.

**Important S3 detail:** `s3:ListBucket` applies to the bucket ARN; `s3:GetObject` and `s3:PutObject` apply to object ARNs.

**Principal**

Example in a resource-based policy or role trust policy:

```json
"Principal": {
  "AWS": "arn:aws:iam::123456789012:root"
}
```

Common principal forms:

- AWS account or IAM role ARN.
- AWS service principal, e.g. `"Service": "ec2.amazonaws.com"`.
- `"*"` for any principal, subject to other policy controls.

**Condition**

Example: allow a request only when it comes from a specified IP address:

```json
"Condition": {
  "IpAddress": {
    "aws:SourceIp": "192.0.2.10/32"
  }
}
```

Common condition operators:

- `StringEquals`, `StringLike`
- `ArnEquals`, `ArnLike`
- `IpAddress`, `NotIpAddress`
- `Bool`
- `DateGreaterThan`, `DateLessThan`
- `NumericLessThan`, `NumericGreaterThan`

A condition operator compares a request context key (such as `aws:SourceIp` or `aws:MultiFactorAuthPresent`) with the specified value.

### Policy evaluation – interview essentials

- By default, requests are implicitly denied.
- An applicable explicit `Deny` overrides an `Allow`.
- An `Allow` is effective only when no applicable explicit Deny or higher-level restriction blocks it.
- Permissions boundaries, SCPs, session policies, and resource policies can also affect the final result.
- `Action` and `Resource` must match the requested operation and resource; conditions must also be satisfied.

## 5. Authentication vs Authorization

| Authentication                            | Authorization                                |
| ----------------------------------------- | -------------------------------------------- |
| Verifies who you are                      | Determines what you can do                   |
| Example: Sign in with credentials and MFA | Example: Permission to start an EC2 instance |

## 6. IAM Roles and Trust Policies

An IAM role provides temporary credentials to a trusted identity.

**Common use cases:**

- EC2 accessing S3.
- Lambda accessing DynamoDB.
- Cross-account access.
- CI/CD pipeline deploying AWS resources.

| Trust policy                    | Permissions policy                             |
| ------------------------------- | ---------------------------------------------- |
| Defines who can assume the role | Defines what the role can do                   |
| Specifies trusted principal     | Specifies allowed/denied actions and resources |

**DevOps best practice:** Attach an IAM role to EC2 or a CI/CD workload instead of storing long-term AWS access keys in code or on servers.

## 7. MFA and Root User Security

- MFA adds an extra verification step during sign-in.
- Enable MFA on the root user and privileged identities.
- Do not use the root user for daily administration.
- Do not create root access keys.
- Follow least privilege.

## 8. IAM Best Practices

1. Grant only the permissions required (least privilege).
2. Prefer roles and temporary credentials over long-term access keys.
3. Enable MFA for privileged access.
4. Avoid using the root user for daily tasks.
5. Use groups to manage permissions for IAM users.
6. Review and remove unused users, roles, policies, and keys.
7. Use CloudTrail to audit AWS API activity.
8. Never hardcode access keys in source code or configuration files.

## 9. IAM vs IAM Identity Center

| IAM                                    | IAM Identity Center                                                                    |
| -------------------------------------- | -------------------------------------------------------------------------------------- |
| Manages IAM users, roles, and policies | Centrally manages workforce access to multiple AWS accounts and supported applications |
| Defines permissions for AWS actions    | Assigns users/groups access through permission sets                                    |

## 10. DevOps Examples

### Example 1: EC2 needs to read from S3

1. Create an IAM role trusted by EC2.
2. Attach a least-privilege S3 read policy.
3. Attach the role to the EC2 instance.
4. The application uses temporary credentials from the role.

### Example 2: Developer needs read-only access to one bucket

1. Create or select the developer's IAM identity/group.
2. Attach a policy with required read actions only.
3. Scope `Resource` to the intended bucket and objects.
4. Require MFA according to the organization's access policy.

### Example 3: CI/CD pipeline deploys AWS resources

Use an IAM role that the pipeline can assume. Grant only the deployment permissions required, and use short-lived credentials rather than static access keys.

## 11. Top IAM DevOps Interview Questions

### Fundamentals

1. What is AWS IAM, and why is it used?
2. What are IAM users, groups, roles, and policies?
3. What is the difference between an IAM user and an IAM role?
4. What is the principle of least privilege?
5. What is the difference between authentication and authorization?
6. What is MFA?

### Policy JSON and Permissions

7. Explain the structure of an IAM policy JSON.
8. What are `Version`, `Statement`, `Sid`, `Effect`, `Action`, and `Resource`?
9. What is the difference between `Action` and `Resource`?
10. What is an ARN? Explain its format.
11. What is the purpose of `Principal` and where is it used?
12. What is `Condition`? Name common condition operators.
13. What is the difference between identity-based and resource-based policies?
14. What is the difference between AWS managed, customer managed, and inline policies?
15. What happens when an explicit Deny and an Allow both apply?
16. What is the difference between a trust policy and a permissions policy?
17. What is the difference between `Action` and `NotAction`?

### DevOps Scenarios

18. An EC2 application needs access to S3. How would you configure it securely?
19. A developer needs read-only access to one S3 bucket. How would you grant it?
20. How would you provide cross-account access using an IAM role?
21. How would you avoid storing AWS access keys in application code?
22. How would you audit who performed an action in an AWS account?
23. How would you troubleshoot an `AccessDenied` error?
24. How would you give a CI/CD pipeline permission to deploy AWS resources securely?
25. How would you manage workforce access across multiple AWS accounts?

## 12. Quick Revision

| Term            | Remember                                    |
| --------------- | ------------------------------------------- |
| IAM             | Controls access to AWS resources            |
| User            | Identity with long-term credentials         |
| Group           | Collection of IAM users                     |
| Role            | Assumed identity with temporary credentials |
| Policy          | JSON document defining permissions          |
| `Effect`      | Allow or Deny                               |
| `Action`      | What operation can be performed             |
| `Resource`    | Which resource the operation applies to     |
| `Principal`   | Who can access a resource                   |
| `Condition`   | Under what request conditions               |
| Trust policy    | Who can assume a role                       |
| MFA             | Extra authentication factor                 |
| Least privilege | Grant only required permissions             |
| CloudTrail      | Records AWS API activity                    |

**Interview focus:** Be confident with policy JSON, policy evaluation, explicit Deny, ARN, trust vs permissions policies, EC2 roles, cross-account access, and CI/CD permissions.
