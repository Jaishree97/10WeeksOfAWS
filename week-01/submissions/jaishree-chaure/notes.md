# Week 1 — Cloud Foundations & IAM

> My notes and interview preparation for Week 1 of #10WeeksOfAWS.

---

# Part 1 — Cloud Foundations

## 1. What is AWS Global Infrastructure?

AWS infrastructure is built using:

- **Regions** — geographic locations containing multiple Availability Zones.
- **Availability Zones (AZs)** — one or more discrete data centers with redundant power, networking, and connectivity within an AWS Region.
- **Edge Locations** — locations used by services such as CloudFront to deliver content closer to users.

### Exam Pointer

- **High Availability** → Design across multiple Availability Zones.
- **Low Latency Content Delivery** → Use CloudFront and Edge Locations.

---

## 2. What is the Shared Responsibility Model?

AWS security is shared between AWS and the customer.

### AWS is responsible for:

**Security of the Cloud**

- Physical data centers
- Networking infrastructure
- Servers and storage hardware
- Global AWS infrastructure
- Hypervisor
- Managed service infrastructure

### Customer is responsible for:

**Security in the Cloud**

- IAM users, groups, and roles
- Application security
- Operating system patches for EC2
- Customer data
- Data encryption
- Security Groups and Network ACLs
- Application configuration

### Simple Line

> **AWS secures the cloud. You secure what you build in the cloud.**

---

## 3. What is the AWS Root User?

The root user is the account owner identity and has full access to the AWS account.

### Best Practices

- Do not use the root user for daily tasks.
- Enable MFA on the root user.
- Do not create root access keys.
- Use IAM identities for regular AWS access.
- Secure the root user credentials carefully.

---

## 4. When should you use CloudFront and Edge Locations?

Use **Amazon CloudFront** when you need:

- Low-latency content delivery
- High performance
- Improved security
- Global content delivery
- Caching of static and dynamic content

### Edge Locations

Edge Locations are locations in the AWS global network where CloudFront can cache and serve content closer to users.

### Simple Example

```text
User
  ↓
CloudFront
  ↓
Edge Location
  ├── Cache Hit  → Cached Content
  │
  └── Cache Miss → Origin
```
### Key Point

> **CloudFront reduces latency by serving cached content closer to users.**

--- 

# Part 2 — IAM Fundamentals

## 1. What is IAM?

**AWS Identity and Access Management (IAM)** controls who can access AWS resources and what actions they can perform.

### IAM Components

- **Users** — long-term IAM identities for people or applications that require AWS access.
- **Groups** — collections of users with common permissions.
- **Roles** — identities that can be assumed to receive temporary permissions.
- **Policies** — JSON documents that define permissions.

> For AWS workloads, IAM roles are generally preferred over IAM users with long-term access keys.

### Simple Formula

> **Identity + Permissions = Access**

---

## 2. What is an IAM User?

An **IAM User** is a long-term identity that represents a person or application requiring AWS access.

### Examples

- `learner-s3` → user can be given S3 read-only access.
- `learner-ec2` → user can be given EC2 read-only access.
- `learner-billing` → user can be given billing view access.

### Important Point

> **Having login access does **not** mean having full AWS access.**

> **Permissions determine what the user can do.**

> **For applications and AWS workloads, prefer **IAM roles** and temporary credentials instead of long-term IAM user access keys whenever possible.**

---

## 3. What is an IAM Group?

An **IAM Group** is a collection of IAM users with common permissions.

### Examples

```text
S3ReadOnlyGroup
        ↓
AmazonS3ReadOnlyAccess
```
```text        
EC2ReadOnlyGroup
        ↓
AmazonEC2ReadOnlyAccess
```

```text
BillingViewGroup
    ↓
AWSBillingReadOnlyAccess
```
### Best Practice

For IAM users, prefer assigning permissions through groups when users share common access requirements instead of attaching policies directly to individual users.

### Benefits

- Easier permission management
- Consistent permissions
- Easier onboarding
- Easier offboarding
- Reduced administrative overhead

---

## 4. What is an IAM Role?

An **IAM Role** is an identity with permissions that can be assumed by a trusted principal to obtain temporary security credentials.

### Common Use Cases

- AWS services
- Cross-account access
- Federated authentication
- GitHub Actions
- Temporary access

### Key Point

> **IAM Role** = An assumable identity that provides temporary permissions through temporary security credentials.

---

## 5. What is an IAM Policy?

An **IAM Policy** is a JSON document that defines permissions for AWS actions and resources.

A policy can specify:

- **Effect** → Allow or Deny
- **Action** → AWS API operation
- **Resource** → AWS resource
- **Condition** → Optional restrictions

### Examples

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```
### Policy Structure

| Element | Meaning |
|---|---|
| **Version** | Policy language version |
| **Statement** | Permission block |
| **Effect** | Allow or Deny |
| **Action** | AWS API operation |
| **Resource** | AWS resource |
| **Condition** | Optional restriction |

### Important

> **An `Allow` statement grants permission, while an explicit `Deny` overrides an `Allow`.**

---

## 6. What are the Types of IAM Policies?

### i. AWS Managed Policy

Created and maintained by AWS.

Example: `AmazonS3ReadOnlyAccess`

### ii. Customer Managed Policy

Created and maintained by you.

Useful when you need reusable custom permissions.

### iii. Inline Policy

A policy embedded directly into a:

- User
- Group
- Role

### Simple Difference

```text
Managed Policy → Reusable and can be attached to multiple identities
Inline Policy  → Embedded directly into one user, group, or role
```

### Best Practice

> **Prefer customer managed policies for reusable custom permissions.**

> **Use inline policies only when the policy must be tightly coupled to a single identity.**

---

## 7. What is Least Privilege?

The Principle of Least Privilege means granting only the minimum permissions required to complete a task.

### Example

If a user only needs to view EC2 instances:

```text
❌ AmazonEC2FullAccess
✅ EC2 Read-Only Access
```
### Benefits

- Reduces security risks
- Prevents accidental changes
- Limits damage from compromised accounts

### Simple Line

> **Give enough access to do the work, but not enough access to create damage.**

---

## 8. What is a Permission Boundary?

A `Permission Boundary` defines the maximum permissions an IAM user or role can have.

It acts like a permission ceiling.

### Important Points

- It does not grant permissions by itself.
- It limits the maximum permissions an identity can receive.
- The effective permissions are limited by the intersection of the identity-based policy and the permissions boundary.

### Example

```text
Identity Policy
       +
Permission Boundary
       ↓
Effective Permissions
```

If the identity policy allows: `s3:GetObject`

and the permission boundary allows: `s3:*`

The user can still only perform: `s3:GetObject`

### Important

> **A permissions boundary does not grant permissions by itself. It only defines the maximum permissions that identity-based policies can grant.**

---

# Part 3 — IAM Interview Questions

## 1. What is the difference between a Region and an Availability Zone?

- **Region** → A geographical area containing multiple Availability Zones.
- **Availability Zone (AZ)** → One or more isolated data centers within a Region.

**Example:**

- Region → `us-east-1`
- Availability Zone → `us-east-1a`

---

## 2. When should you use CloudFront and Edge Locations?

Use **Amazon CloudFront** when you need:

- Low-latency content delivery
- High performance
- Global content delivery
- Caching of static and dynamic content
- Secure delivery using HTTPS, AWS WAF, and AWS Shield

**Edge Locations** are locations in the AWS global network where CloudFront caches and serves content closer to users.

> **Key Point:** CloudFront reduces latency by serving cached content closer to users.

---

## 3. What does AWS manage in the Shared Responsibility Model?

AWS manages **Security of the Cloud**, including:

- Physical data centers
- Networking infrastructure
- Servers and storage hardware
- Global AWS infrastructure
- Hypervisor
- Managed service infrastructure

---

## 4. What does the customer manage in the Shared Responsibility Model?

Customers manage **Security in the Cloud**, including:

- IAM users, groups, and roles
- Application security
- Operating system patches for EC2
- Customer data
- Data encryption
- Security Groups and Network ACLs
- Application configuration

---

## 5. Why should the root user not be used daily?

The root user:

- Has unrestricted access to the AWS account.
- Cannot have its permissions restricted.
- Is a high-value target for attackers.

### Best Practice

> Use the root user only for tasks that specifically require root access.

---

## 6. Why is MFA important for the root user?

**Multi-Factor Authentication (MFA):**

- Adds an additional authentication factor.
- Protects against stolen passwords.
- Reduces the risk of unauthorized access.

### Best Practice

> Always enable MFA on the root user.

---

## 7. What is the difference between an IAM User and an IAM Role?

### IAM User

- Long-term identity for a person or application.
- Can have long-term credentials.
- Can have console access and/or access keys.
- Permissions can be attached directly or through groups.

### IAM Role

- Identity with permissions that can be assumed.
- Does not have long-term credentials.
- Provides temporary security credentials.
- Can be assumed by users, AWS services, or external identities.
- Commonly used for cross-account access, AWS services, and federated authentication.

### Key Difference

> **IAM User** → Long-term identity with long-term credentials.
>
> **IAM Role** → Assumable identity that provides temporary credentials.

---

## 8. Why should permissions be attached to groups instead of individual users?

Using groups:

- Simplifies permission management.
- Keeps permissions consistent.
- Makes onboarding and offboarding easier.
- Reduces administrative overhead.

> **Manage common permissions through groups instead of attaching them individually to each user.**

---

## 9. What is Least Privilege?

The **Principle of Least Privilege** means granting only the minimum permissions required to perform a task.

### Benefits

- Reduces security risks.
- Prevents accidental changes.
- Limits damage from compromised accounts.

> **Give only the access required to do the job.**

---

## 10. What does an IAM Policy contain?

An IAM policy is a **JSON document** that defines permissions.

Common elements:

- **Effect** → `Allow` or `Deny`
- **Action** → AWS API operation
- **Resource** → AWS resource
- **Condition** → Optional restriction

### Example

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```
> Explicit Deny overrides Allow.

---

## 11. What is the difference between a Managed Policy and an Inline Policy?

### Managed Policy

- Standalone policy.
- Reusable across multiple identities.
- Can be AWS managed or customer managed.
- Easier to maintain.

### Inline Policy

- Embedded directly into one user, group, or role.
- Not reusable.
- Tightly coupled to that identity.

### Key Difference

> **Managed Policy** → Standalone and reusable.
>
> **Inline Policy** → Embedded into one identity.

---

## 12. What does a Permission Boundary do?

A **Permission Boundary** defines the maximum permissions an IAM user or role can receive.

```text
Identity Policy
       +
Permission Boundary
       ↓
Effective Permissions
```

### Important
- Does not grant permissions by itself.
- Acts as a permission ceiling.
- Limits the permissions that identity-based policies can grant.

> **Permission Boundary = Maximum permissions, not granted permissions.**

---

## 13. Why are temporary credentials safer than long-lived access keys?

Temporary credentials:

- Expire automatically.
- Reduce the impact of credential leaks.
- Reduce the need for manual rotation.
- Are issued through **AWS STS**.
- Avoid the need to store long-term credentials in applications or CI/CD pipelines.

### Long-Lived Access Keys

- Remain valid until rotated or deleted.
- Have a larger exposure window if compromised.
- Require secure storage and rotation.

### Key Difference

> **Temporary credentials are short-lived and automatically expire, reducing the security risk of credential exposure.**

---

## 14. What problem does GitHub OIDC solve?

**GitHub OpenID Connect (OIDC)** allows GitHub Actions to access AWS without storing long-lived AWS access keys in GitHub Secrets.

### OIDC Flow

```text
GitHub Actions
      ↓
OIDC Token
      ↓
AWS IAM Role
      ↓
Temporary Credentials
      ↓
AWS Resources
```
### Benefits
- No long-lived AWS keys in GitHub Secrets.
- Temporary AWS credentials.
- Better security.
- Reduced credential management.

---

## 15. What does AWS STS provide?

**AWS Security Token Service (STS)** provides temporary security credentials.

### Common STS Operations

- `AssumeRole`
- `AssumeRoleWithWebIdentity`
- `GetSessionToken`
- `GetCallerIdentity`

### Benefits

- Temporary credentials
- Cross-account access
- Federated authentication
- Improved security
- Reduced need for long-term access keys








