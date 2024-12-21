# AWS_IAM
This project demonstrates best practices for managing AWS Identity and Access Management (IAM) roles, users, policies, and groups. It provides a working example of creating and managing IAM resources using Infrastructure as Code (IaC) tools like AWS CloudFormation or Terraform. 
IAM-Access-Control-Demo/
├── policies/
│   ├── admin-policy.json
│   ├── developer-policy.json
│   ├── auditor-policy.json
├── terraform/
│   ├── main.tf
├── scripts/
│   ├── create-user.sh
│   ├── assign-group.sh
├── docs/
│   ├── IAM-best-practices.md
│   ├── RBAC-examples.md
└── README.md
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "lambda:InvokeFunction",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudtrail:LookupEvents",
        "s3:GetObject",
        "logs:GetLogEvents",
        "billing:Get*",
        "billing:List*"
      ],
      "Resource": "*"
    }
  ]
}
provider "aws" {
  region = "us-east-1"
}

resource "aws_iam_user" "developer_user" {
  name = "developer_user"
}

resource "aws_iam_group" "developers" {
  name = "developers"
}

resource "aws_iam_group_policy_attachment" "developers_policy_attachment" {
  group      = aws_iam_group.developers.name
  policy_arn = aws_iam_policy.developer_policy.arn
}

resource "aws_iam_policy" "developer_policy" {
  name        = "developer_policy"
  description = "IAM policy for developers"
  policy      = file("../policies/developer-policy.json")
}

resource "aws_iam_user_group_membership" "developer_membership" {
  user = aws_iam_user.developer_user.name
  groups = [
    aws_iam_group.developers.name
  ]
}
#!/bin/bash
# Create an IAM user
aws iam create-user --user-name $1
#!/bin/bash
# Assign a user to a group
aws iam add-user-to-group --user-name $1 --group-name $2
# IAM Best Practices

1. Follow the principle of least privilege.
2. Use IAM roles instead of long-term access keys.
3. Enable MFA for all users.
4. Regularly audit permissions and access logs.
# Role-Based Access Control Examples

## Lambda Execution Role
```yaml
Version: "2012-10-17"
Statement:
  - Effect: Allow
    Action:
      - logs:CreateLogGroup
      - logs:CreateLogStream
      - logs:PutLogEvents
    Resource: "arn:aws:logs:*:*:*"
Version: "2012-10-17"
Statement:
  - Effect: Allow
    Action:
      - s3:PutObject
      - s3:GetObject
    Resource: "arn:aws:s3:::your-bucket-name/*"
