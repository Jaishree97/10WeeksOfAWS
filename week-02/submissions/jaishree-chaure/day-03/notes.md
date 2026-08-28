# IAM Roles and AWS STS

## IAM Role

An IAM role is an AWS identity that contains permissions but does not use long-term credentials.

A trusted principal can assume the role and receive temporary security credentials to access AWS resources.

IAM roles are commonly used by:

- EC2 instances
- Lambda functions
- AWS services
- Applications
- External identities

---

## Trust Policy vs Permission Policy

An IAM role uses two different policies with separate purposes:

| **Policy** | **Purpose** | **Answers** |
|---|---|---|
| Trust Policy | Defines who can assume the role | Who can access this role? |
| Permission Policy | Defines what actions the role can perform | What can this role do? |

---

## Trust Policy

A trust policy defines the trusted principal that can assume an IAM role.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
This allows the EC2 service to assume the role.

A trust policy does not grant access to AWS resources.

---

## Permission Policy

A permission policy defines the AWS actions and resources available after the role is assumed.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllowedBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::ec2-s3-read-lab-jaishree-chaure-2026"
    },
    {
      "Sid": "ReadObjectsInAllowedBucket",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::ec2-s3-read-lab-jaishree-chaure-2026/*"
    }
  ]
}
```
This grants only the required read access to the specific S3 bucket.

---

## AWS STS AssumeRole

AWS Security Token Service (STS) provides temporary credentials when a trusted principal assumes an IAM role.

The process:

1. A principal requests to assume a role.
2. AWS evaluates the role's trust policy.
3. The role is assumed if the principal is trusted.
4. STS creates temporary security credentials.
5. The credentials are used to access AWS resources.
6. The credentials expire after the session duration.

---

## Temporary Credentials

STS provides temporary credentials containing:

- Access key ID
- Secret access key
- Session token
- Expiration time

Temporary credentials reduce the risk of long-term credential exposure because they automatically expire.

---

## EC2 Roles and Instance Profiles

An EC2 instance receives an IAM role through an instance profile.

The relationship:

```text
IAM Role
    |
Instance Profile
    |
EC2 Instance
```

The instance profile makes the IAM role available to the EC2 instance.

EC2 workloads can automatically obtain temporary credentials through the **`EC2 Instance Metadata Service (IMDS)`**.

The AWS CLI and SDKs can automatically use and refresh these credentials.

---

## Avoid Long-Term Access Keys

Avoid storing AWS access keys in:

Source code
Environment files
User data scripts
AMIs
Shell history
Public repositories

For AWS workloads such as EC2, prefer **IAM Roles and temporary credentials.**

---

## EC2-to-S3 Role-Based Access

An EC2 workload can access S3 without storing AWS access keys:

```text
EC2
 |
 v
Instance Profile
 |
 v
IAM Role
 |
 v
Temporary Credentials
 |
 v
S3
```
In this lab, the EC2 role was limited to:

```text
s3:ListBucket
s3:GetObject
```
This allowed the instance to read `day3-test.txt` while preventing unnecessary write and delete access.

---

## Cross-Service Role Assumption

Cross-service role assumption occurs when an AWS service uses an IAM role to access another AWS service.

### Examples

- **EC2** accessing **S3**
- **Lambda** writing logs to **CloudWatch**
- **ECS** retrieving secrets from **Secrets Manager**
- **Step Functions** invoking **Lambda**

Two things are required:

1. The **trust policy** must allow the service to assume the role.
2. The **permission policy** must allow the required actions.

---

## Principle of Least Privilege

The principle of least privilege means granting only the permissions required for a specific task.

For an application that only needs to read from S3:

**Allowed:**

```text
s3:ListBucket
s3:GetObject
```
**Not required:**

```text
s3:PutObject
s3:DeleteObject
```
In this lab, denied operations returned `AccessDenied`, confirming that the policy boundaries were working as intended.

---

## S3 Bucket vs Object Permissions

S3 permissions can apply to different resource types.

For example:

```text
s3:ListBucket
```
uses the bucket ARN:

```text
arn:aws:s3:::bucket-name
```
While:

```text
s3:GetObject
```
uses an object ARN:

```text
arn:aws:s3:::bucket-name/*
```
Understanding this distinction is important when creating least-privilege S3 policies.

---

## Verification Commands

### Check Current Identity

```bash
aws sts get-caller-identity
```
Shows the AWS identity currently being used.

For an EC2 instance using a role, the result should show an assumed-role identity.

---

## Check Credential Source

```bash
aws configure list
```
Shows where the AWS CLI is obtaining its credentials.

For this lab, the credentials were provided through the EC2 IAM role and IMDS.

---

## IAM Authorization Testing

IAM policies should be validated with both successful and denied operations.

**Allowed**

```text
List allowed bucket  → SUCCESS
Read day3-test.txt   → SUCCESS
```

**Denied**

```text
Access other bucket  → AccessDenied
Upload object        → AccessDenied
Delete object        → AccessDenied
```

Testing both outcomes helps verify that permissions are neither too broad nor too restrictive.

---

## IAM Troubleshooting

An `AccessDenied` error does not always mean the role is missing entirely.

Check:

1. Which IAM identity is being used?
2. Is the correct role attached?
3. Is the requested action allowed?
4. Is the resource ARN correct?
5. Is there an explicit Deny?
6. Are other policies affecting the request?

In this lab, changing:

```text
s3:GetObject
```
to:

```text
s3:GetObjectVersion
```
caused an `AccessDenied` error because the requested `s3:GetObject` action was no longer allowed.

---

## Key Concepts

| **Concept**       | **Meaning**                                      |
| ----------------- | ------------------------------------------------ |
| IAM Role          | Provides a secure identity for AWS workloads     |
| Trust Policy      | Defines who can assume the role                  |
| Permission Policy | Defines allowed actions and resources            |
| AWS STS           | Provides temporary security credentials          |
| Instance Profile  | Connects an IAM role to EC2                      |
| IMDS              | Provides EC2 workloads access to role credentials |
| Least Privilege   | Grants only the permissions required             |
| AccessDenied      | Indicates an authorization failure               |

---

## Key Line

1. **Trust Policy answers WHO.**

2. **Permission Policy answers WHAT.**

3. **STS provides temporary credentials.**

4. **Instance Profiles make IAM roles available to EC2.**

5. **Least Privilege limits access to only what is required.**

---

## Interview Questions and Answers

### 1. Why should an application avoid storing access keys?

- Long-term access keys can be leaked or stolen. IAM Roles provide temporary credentials that expire automatically.

---

### 2. Who is allowed to assume a role?

- Only principals listed in the role's trust policy, such as AWS services, IAM roles, IAM users, accounts, or federated identities.

---

### 3. What is the role allowed to do after it is assumed?

- It can perform only the actions allowed by its permission policies, subject to other applicable AWS authorization controls.

---

### 4. Why do temporary credentials expire?

- Expiration limits the window in which compromised credentials can be misused and reduces the need to manage long-lived credentials.

---

### 5. Does an EC2 trust policy grant permission to read an S3 object?

- No. The trust policy only determines who can assume the role. S3 access is granted through permission policies.

---

### 6. What must change if Lambda needs to assume the role instead of EC2?

- The trust policy must trust the Lambda service principal `lambda.amazonaws.com` instead of `ec2.amazonaws.com`.

---

### 7. Why is an instance role safer than storing IAM user keys on EC2?

- EC2 can obtain temporary credentials through its IAM role instead of storing long-term access keys on the instance.

---

### 8. What happens when temporary credentials expire?

- The expired credentials can no longer be used. The AWS CLI or SDK obtains refreshed temporary credentials through the role when needed.

