# 05 – AWS S3 (Simple Storage Service)

## 1. What is Amazon S3?

**Purpose:** Amazon S3 is a scalable object storage service used to store and retrieve data.

**Key Points:**
- Virtually unlimited storage capacity; highly durable and cost-effective.
- Offers multiple storage classes for different access and cost requirements.
- Supports encryption for data at rest and HTTPS for data in transit.
- Versioning helps preserve, retrieve, and restore previous object versions.
- Integrates with AWS services such as Lambda, CloudFront, and CloudTrail.

## 2. Core S3 Concepts

| Concept | Purpose |
|---|---|
| Bucket | Container for storing objects; bucket names are globally unique within an AWS partition |
| Object | File and its metadata stored in S3 |
| Key | Unique name/path identifying an object within a bucket |
| Region | AWS Region where the bucket is created |
| Versioning | Keeps multiple versions of an object |
| Lifecycle Policy | Automatically transitions or expires objects |
| Bucket Policy | Resource-based JSON policy controlling bucket access |
| Access Point | Named network endpoint with its own access policy for a bucket |
| Multipart Upload | Uploads large objects in parts |

**Remember:** Bucket = container · Object = data/file · Key = object name.

## 3. S3 Storage Classes

| Storage class | Typical use |
|---|---|
| S3 Standard | Frequently accessed data |
| S3 Intelligent-Tiering | Data with changing or unknown access patterns |
| S3 Standard-IA | Infrequently accessed data that needs fast retrieval |
| S3 One Zone-IA | Infrequently accessed, re-creatable data stored in one AZ |
| S3 Glacier Instant Retrieval | Archive data needing millisecond retrieval |
| S3 Glacier Flexible Retrieval | Archive data with retrieval time from minutes to hours |
| S3 Glacier Deep Archive | Long-term archive with hours-long retrieval |

**Interview point:** Choose a storage class based on access frequency, retrieval time, resilience needs, and cost.

## 4. S3 Security

- **Block Public Access:** Helps prevent accidental public exposure.
- **Bucket Policy:** Controls access at bucket level using JSON.
- **IAM Policy:** Grants permissions to IAM identities.
- **ACLs:** Legacy access-control mechanism; keep disabled where not required.
- **Encryption at rest:** SSE-S3, SSE-KMS, or SSE-C.
- **Encryption in transit:** Use HTTPS/TLS.
- **IAM Role:** Lets EC2, Lambda, or other workloads access S3 without hardcoded credentials.

### Bucket Policy Example

Allow a specific IAM role to read objects from a bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowRoleToReadObjects",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/AppS3ReadRole"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::company-data/*"
    }
  ]
}
```

## 5. Versioning and Lifecycle Management

### Versioning
- Keeps multiple versions of an object in the same bucket.
- Helps recover from accidental overwrite or deletion.
- A delete operation on a versioned bucket normally adds a delete marker.
- Versioning must be enabled before versions can be retained.

### Lifecycle Policy
Automates object transitions and deletion.

Example:
- Move older objects to S3 Standard-IA.
- Transition archive data to Glacier.
- Expire old objects after a defined retention period.
- Clean up incomplete multipart uploads.

## 6. S3 Replication

- **Same-Region Replication (SRR):** Replicates objects to another bucket in the same Region.
- **Cross-Region Replication (CRR):** Replicates objects to a bucket in another Region.

Common use cases:
- Disaster recovery.
- Compliance requirements.
- Keeping data closer to users.

**Note:** Replication requires versioning on both source and destination buckets. Existing objects need a separate Batch Replication operation if they must be copied.

## 7. S3 Integration with Other AWS Services

| Service | Integration |
|---|---|
| CloudFront | Delivers S3 content globally through a CDN |
| Lambda | Triggers code when configured S3 events occur |
| CloudTrail | Records S3 API activity |
| Athena | Queries supported data directly in S3 |
| AWS Backup | Centralized backup management for supported S3 data |
| EC2 / IAM Roles | Applications access S3 securely using role permissions |

## 8. Common AWS CLI Commands

```bash
# List buckets
aws s3 ls

# Create a bucket (specify a Region)
aws s3 mb s3://my-unique-bucket --region ap-south-1

# Upload a file
aws s3 cp file.txt s3://my-unique-bucket/

# Download a file
aws s3 cp s3://my-unique-bucket/file.txt .

# Copy a directory recursively
aws s3 cp ./app s3://my-unique-bucket/app/ --recursive

# Sync a directory
aws s3 sync ./app s3://my-unique-bucket/app/

# Remove an object
aws s3 rm s3://my-unique-bucket/file.txt

# List objects
aws s3 ls s3://my-unique-bucket/
```

## 9. Top S3 DevOps Interview Questions

### Fundamentals
1. What is Amazon S3, and where is it used?
2. What are buckets, objects, and keys?
3. What are the different S3 storage classes?
4. What is the difference between S3 Standard and S3 Intelligent-Tiering?
5. What is S3 Versioning?
6. What is an S3 Lifecycle Policy?

### Security and Access
7. What is the difference between an IAM policy and an S3 bucket policy?
8. How do you make an S3 bucket private?
9. What is S3 Block Public Access?
10. How does an EC2 instance securely access an S3 bucket?
11. What is the difference between SSE-S3 and SSE-KMS?
12. How do you encrypt data in transit and at rest?

### Replication and Integration
13. What is the difference between SRR and CRR?
14. What are the prerequisites for S3 replication?
15. How do you host static website content using S3?
16. How does CloudFront integrate with S3?
17. How can Lambda be triggered by an S3 upload event?

### Scenario-Based Questions
18. A user accidentally deletes an S3 object. How can you recover it?
19. An EC2 application receives `AccessDenied` while accessing S3. How do you troubleshoot?
20. How would you store application logs in S3 cost-effectively?
21. How would you design S3 storage for long-term backups?
22. How do you prevent accidental public access to an S3 bucket?
23. How would you replicate critical data to another AWS Region?
24. How would you upload and manage very large files in S3?

## 10. Quick Revision

- **S3:** Scalable object storage.
- **Bucket:** Container for objects.
- **Object:** Stored file and metadata.
- **Key:** Object identifier within a bucket.
- **Storage Class:** Controls storage cost and retrieval characteristics.
- **Versioning:** Preserves object versions.
- **Lifecycle:** Automates transition and expiration.
- **Bucket Policy:** Resource-based access policy.
- **SSE:** Server-side encryption at rest.
- **CRR / SRR:** Cross-Region / Same-Region Replication.
- **CloudFront:** CDN for delivering content.
- **Multipart Upload:** Uploads large objects in parts.

**Interview focus:** Storage classes, bucket policies vs IAM policies, encryption, versioning, lifecycle rules, replication, CLI commands, and `AccessDenied` troubleshooting.
