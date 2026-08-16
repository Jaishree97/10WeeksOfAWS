# Week 1 - IAM & AWS Security Challenge

## Name

Jaishree Chaure

## Objective

Build a strong foundation in AWS security by securing the root user, configuring cost controls, and exploring AWS monitoring and auditing services.

---

## Tasks Completed

- [x] Reviewed the weekly AWS security content
- [x] Completed hands-on AWS security labs
- [x] Enabled Root User MFA
- [x] Configured AWS Billing Budget and alerts
- [x] Reviewed CloudTrail `ConsoleLogin` activity
- [x] Configured IAM Access Analyzer
- [x] Enabled CloudWatch Billing Alerts
- [x] Created a CloudWatch Billing Alarm
- [x] Configured Amazon SNS email notifications
- [x] Added screenshots as proof
- [x] Reviewed AWS security recommendations

---

## Lab 1 - Secure Root User

### Objective

Secure the AWS root user by enabling Multi-Factor Authentication (MFA) and following AWS security best practices.

### Steps

1. Created and logged in to the AWS account.
2. Opened `IAM` from the AWS Console.
3. Reviewed **Security `Recommendations` and MFA settings.
4. Enabled `MFA` for the root user.
5. Verified that MFA was successfully enabled.
6. Verified that the root user has `no active access keys`.
7. Reviewed the IAM security recommendations.

### Why Should the AWS Root User Not Be Used Daily?

- The root user has full access to the entire AWS account.
- Using the root user for everyday tasks increases the risk of accidental changes.
- If the root account is compromised, an attacker can gain complete control of the AWS account.
- IAM users and roles should be used for regular AWS operations with only the required permissions.
- The root user should be used only for tasks that specifically require root-level access.
- MFA should always be enabled on the root user for an additional layer of security.

### Result

- Root User MFA: `Enabled`
- Root User Access Keys: `None`
- IAM Security Recommendations: `Reviewed`

> **Best Practice:** Protect the root user with MFA and avoid using it for daily AWS operations.

---

## Topics Practiced

- Root User MFA
- IAM Dashboard & Security Recommendations
- AWS Billing Budget
- Budget Alerts
- CloudTrail Console Login Monitoring
- IAM Access Analyzer
- CloudWatch Billing Alerts
- CloudWatch Billing Alarm
- Amazon SNS Email Notifications
- Least Privilege

---

## What I Learned

This challenge helped me understand the basic security controls that should be configured when working with AWS.

### IAM Security

- Enabled MFA for the AWS root user.
- Verified that the root user has no active access keys.
- Reviewed IAM security recommendations.
- Learned why the root user should not be used for daily operations.
- Understood the importance of the `principle of least privilege`.

### Cost Management

- Created a monthly AWS budget of `$5`.
- Configured budget alerts for actual and forecasted costs.
- Enabled `Receive CloudWatch billing alerts`.
- Created a CloudWatch billing alarm using the `EstimatedCharges` metric.
- Learned how AWS Budgets and CloudWatch billing alarms help monitor unexpected costs.

### CloudWatch Billing Alarm Prerequisites

Before creating the CloudWatch billing alarm, I completed the required setup:

1. Opened `Billing and Cost Management`.
2. Opened `Billing Preferences`.
3. Enabled `Receive CloudWatch billing alerts`.
4. Created the billing alarm in `US East (N. Virginia) - us-east-1`, where AWS billing metrics are available.

### Monitoring & Auditing

- Reviewed a `ConsoleLogin` event in AWS CloudTrail.
- Created an IAM Access Analyzer.
- Verified that the Access Analyzer had `0 findings`.
- Configured Amazon SNS email subscriptions for billing notifications.
- Verified that the SNS subscriptions were `Confirmed`.

---

## Where I Got Stuck

No major blocker.

The main learning point was understanding the difference between AWS cost monitoring and security monitoring services:

- `AWS Budgets` → Monitors spending against configured budget thresholds.
- `CloudWatch Billing Alarms` → Monitors billing metrics such as `EstimatedCharges`.
- `CloudTrail` → Records AWS account activity, including console and API events.
- `IAM Access Analyzer` → Helps identify resources that allow external access.
- `Amazon SNS` → Delivers notifications to subscribed endpoints such as email.

---

## Screenshots Added

### 1. Root MFA Enabled

![Root MFA Enabled](./screenshots/01-root-mfa-enabled.png)

### 2. IAM Security Dashboard

![IAM Dashboard](./screenshots/02-IAM-dashboard.png)

### 3. AWS Monthly Budget

![Create Budget](./screenshots/03-create-budget.png)

### 4. Budget Alerts

![Budget Alerts](./screenshots/04-budget-alerts-80-forecasted-100.png)

### 5. CloudTrail Console Login Event

![CloudTrail Console Login](./screenshots/05-cloudtrail-console-login-event.png)

### 6. IAM Access Analyzer

![IAM Access Analyzer](./screenshots/06-access-analyzer.png)

### 7. CloudWatch Billing Alerts

![CloudWatch Billing Alerts](./screenshots/07-cloudwatch-billing-alerts-enabled.png)

### 8. CloudWatch Billing Alarm & SNS

![CloudWatch Billing Alarm](./screenshots/08-cloudwatch-billing-alarm-sns-confirmed.png)

---

## Key Takeaway

**Security starts with controlling access, monitoring activity, and preventing unexpected costs.**

The most important lesson from this challenge was:

> **Least privilege means giving only the permissions required to perform a task — nothing extra.**

I also learned how `IAM, CloudTrail, IAM Access Analyzer, CloudWatch, Amazon SNS, and AWS Budgets` work together to improve the security, monitoring, and cost visibility of an AWS environment.

---

## Skills Practiced

`AWS IAM` `MFA` `AWS Budgets` `CloudTrail` `IAM Access Analyzer` `CloudWatch` `Amazon SNS` `Security Monitoring` `Cost Management` `Least Privilege`

---

## Lab 1 - S3 Read-Only Access

Create:

- Group: `S3ReadOnlyGroup`
- Policy: `AmazonS3ReadOnlyAccess`
- User: `learner-s3`

Test:

- Sign in as `learner-s3`.
- Confirm the user can view S3.
- Try creating or deleting something. It should fail.
- Capture the `Access Denied` result.

## Lab 2 - EC2 Read-Only Access

Create:

- Group: `EC2ReadOnlyGroup`
- Policy: `AmazonEC2ReadOnlyAccess`
- User: `learner-ec2`

Test:

- Sign in as `learner-ec2`.
- Confirm the user can open the EC2 dashboard.
- Confirm the user cannot create or terminate instances.

## Lab 3 - Billing Read-Only Access

Create:

- Group: `BillingViewGroup`
- Policy: `AWSBillingReadOnlyAccess`
- User: `learner-billing`

Test:

- Sign in as `learner-billing`.
- Confirm the user can view the Billing Dashboard.
- Confirm the user cannot manage unrelated AWS services.

## Lab 4 - Custom S3 Read-Only Policy

Create a customer managed policy:

```text
CustomS3ReadOnlyTrainingPolicy
```

Use this structure and replace `YOUR-BUCKET-NAME`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    { "Effect": "Allow", "Action": ["s3:ListAllMyBuckets"], "Resource": "*" },
    { "Effect": "Allow", "Action": ["s3:ListBucket"], "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME" },
    { "Effect": "Allow", "Action": ["s3:GetObject"], "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*" }
  ]
}
```

Add these in your submission:

- Screenshots of group, user, and attached policy.
- Screenshot of allowed access.
- Screenshot or note for denied access.
- Policy JSON file in your submission folder.

## If You Get Stuck

Submit the part you completed. Add a note like:

```text
I completed S3 read-only access. I got stuck while testing EC2 denied access.